# Review checklist — redis-performance/openmessaging-benchmark

Every item here is grounded either in a verifiable structural fact about this repository (module layout,
what CI already runs) or in generic, defensible Java/Maven/messaging-benchmark practice. None of it is
"a maintainer has flagged this before" — see `mined-history.md` for why that category is empty for this
fork. Work through what's relevant to the PR at hand; skip categories the PR doesn't touch rather than
padding the review with irrelevant boilerplate.

## 1. What CI already covers — don't re-litigate it

`.github/workflows/pr-build-and-test.yml` runs `mvn --no-transfer-progress --batch-mode verify` on every
PR against `master`. Per this repo's own `README.md`, the Maven `verify` phase already wires in
Checkstyle, Spotless, and Jacoco coverage checks. That means:
- Pure formatting/style issues a linter would catch are already gated mechanically. Don't spend review
  attention restating what `mvn verify` will already fail on.
- Coverage regressions are also gated by Jacoco. A PR that removes tests without removing the code they
  covered will likely fail CI on its own; verify the CI result before manually flagging a coverage gap.
- What CI does *not* verify: runtime correctness of new driver/producer/consumer logic, resource
  lifecycle correctness, or cross-module consistency in a multi-module reactor build. That's where manual
  review attention actually adds value here.

## 2. Multi-module structure — driver-api contract consistency

This is a multi-module Maven project (`driver-api`, and one `driver-<system>` module per messaging
system it benchmarks, including `driver-redis`). Any PR that:
- adds or changes a `driver-*` module should implement the same interfaces/lifecycle `driver-api` defines,
  and should look structurally similar to its sibling drivers (e.g. does it define a
  `<System>BenchmarkDriver`, a config class, and producer/consumer wrappers the way `driver-kafka` or
  `driver-pulsar` do?) — an inconsistent shape is a real maintenance cost in a reactor project like this.
- bumps a dependency inside one driver module's own `pom.xml` should be checked against the parent POM
  for a version already pinned there, to avoid two modules silently resolving different versions of a
  shared transitive dependency.

## 3. driver-redis specifics

`driver-redis` is Jedis-based (bumped to v5.0.0 in this fork's own `f2a4e8c`, its only dependency-bump
commit to date). If a PR touches this module:
- Check whether it's still using the Jedis client idioms current at v5.x (connection pooling via
  `JedisPool`/`JedisPooled`, not older deprecated APIs) rather than reverting to patterns from the
  pre-v5.0.0 Jedis API this fork already moved away from.
- Check that Redis connections/pools opened in producer or consumer setup are actually closed/shut down
  on benchmark teardown — a benchmark harness that leaks connections under load will produce misleading
  throughput/latency numbers, which is a correctness bug for a benchmarking tool specifically, not just
  a resource-hygiene nitpick.

## 4. Resource lifecycle and thread-safety (not caught by CI)

Producer/consumer benchmark code in this project runs under load with many threads. For any new or
changed adapter logic, check:
- Are executors, thread pools, or client connections created in setup actually torn down in
  teardown/close, on both the success and exception paths?
- Is shared mutable state (counters, stats accumulators) across producer/consumer threads properly
  synchronized or using appropriate concurrent data structures, rather than assumed to be safe?
- Does error handling in the hot path (send/receive) avoid silently swallowing exceptions in a way that
  would understate error rates in benchmark results?

## 5. PR description quality

There's no fork-local norm to compare against (zero prior PRs). Judge on general merit: does the
description explain what changed and why, and is there any indication of how it was tested (a local run
against a real broker/Redis instance, not just "compiles")? If a PR changes benchmark-affecting logic
(timing, connection handling, batching) with zero test evidence, that's worth asking about directly —
this project's entire purpose is producing trustworthy numbers, so an unverified change to the
measurement path is higher-stakes than an unverified change to, say, documentation.

## 6. Scope and hygiene

Generic but always worth checking: no unrelated reformatting bundled into a functional change (harder to
review, and this repo's CI-enforced Spotless config means unrelated reformatting is also likely to be
its own no-op or conflict), no dead/commented-out code, and any new dependency added to a module's
`pom.xml` is one the PR description actually explains the need for.
