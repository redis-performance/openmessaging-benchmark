---
name: openmessaging-benchmark-maintainer-review
description: Review a redis-performance/openmessaging-benchmark pull request, branch, or diff. This fork has NO independent review history at all to ground a "maintainer voice" in — see the honesty note below — so this skill is a generic, honest checklist grounded in the repo's actual structure (Java/Maven, multi-module drivers, the driver-redis module, and the CI it already runs) rather than invented precedent. Use this whenever asked to review an openmessaging-benchmark PR "like a maintainer would," whether a PR would pass real review, or wants an openmessaging-benchmark-specific pre-merge check. Prefer this over generic Java code-review advice for this repo because it at least reflects the real (empty) state of this fork's history and its real CI/build setup, instead of pretending otherwise.
---

# openmessaging-benchmark maintainer-style review

## Honesty note — read this first

This skill is adapted from equivalent skills built for other `redis-performance` forks (e.g.
`redisbench-admin`, `go-ycsb`), which had at least *some* real PR/issue review history to mine —
down to a single silent-approval reviewer in the thinnest case. **`redis-performance/openmessaging-benchmark`
has none of that.** As mined on 2026-08-27, scoped strictly to this fork under the `redis-performance`
org (never upstream `openmessaging/benchmark`'s own history, per this skill's own instructions):

- **Zero pull requests, ever**, open or closed (`gh pr list --repo redis-performance/openmessaging-benchmark
  --state all` returns an empty array).
- **Issues are disabled entirely** on this repository — there is no issue history to mine either, and
  `claude-issue-triage.yml` currently has nothing to trigger on. It's included for parity with other
  `redis-performance` forks and in case issues are ever enabled — see the PR description that shipped
  this skill.
- **No `AGENTS.md`, no `CONTRIBUTING.md`** exist anywhere in this fork's history (both 404 on the GitHub
  Contents API against the default branch). There is no written project policy to cite either.
- This fork is a **dormant passthrough fork**: its `master` is frozen at `f2a4e8c` (2023-09-07) while
  upstream `openmessaging/benchmark` has continued on to commit `5b1fa70` and beyond (PR #443 and later,
  as of this mining). The fork has not been synced with upstream in roughly three years.
- Every commit `redis-performance` has ever made to this fork was **pushed directly to `master`**, with
  no PR in between: `854f73d` ("Initial Redis support (#196)", 2021-09-24) and `f2a4e8c` ("Bumped Jedis
  to v5.0.0 (#390)", 2023-09-07) — both authored by the same person (Filipe Oliveira). The `#196`/`#390`
  numbers in those commit messages are the *upstream* project's PR numbers from when these changes were
  first contributed upstream, not PRs against this fork — don't mistake them for this fork's own review
  history.

There is no maintainer "voice" to imitate here in any sense — not even a single recorded silent approval.
**Do not invent one.** What real signal *does* exist is entirely structural, not behavioral: this is a
multi-module Java/Maven project, `redis-performance`'s own interest in it is specifically the
`driver-redis` module (Jedis-based), and CI on `master`/PRs already runs a real `mvn verify` (checkstyle,
spotless, and Jacoco coverage are wired into the Maven build per this repo's own `README.md`) — unlike
some other `redis-performance` forks where CI does nothing beyond a bare compile. Use those facts. Do not
manufacture nitpicks, quotes, or a maintainer personality to make this look more thorough than the real
history supports — see `references/mined-history.md` for the full accounting and
`references/review-checklist.md` for the actual checklist to work.

## Process

1. **Get the material.** `gh pr view <n> --repo redis-performance/openmessaging-benchmark
   --json body,commits,files,author` and `gh pr diff <n> --repo redis-performance/openmessaging-benchmark`.
   Read the PR description first. There is no established norm for what a "good" PR description looks
   like on this fork (zero PRs to date), so judge it on its own merits (does it explain what changed and
   why, is there any test evidence) rather than comparing it to a fork convention that doesn't exist.

2. **Work the checklist** in `references/review-checklist.md`. Every item there is either (a) a structural
   fact about this repo you can verify directly (module layout, what `mvn verify` already enforces,
   `driver-redis`'s own conventions) or (b) generic, defensible Java/Maven/messaging-benchmark practice —
   never "a maintainer has flagged this before," because none ever has.

3. **Because CI here already runs `mvn verify` (checkstyle + spotless + Jacoco), don't re-litigate pure
   style/formatting** — that's enforced mechanically and flagging it is noise. Focus review attention on
   what CI can't catch: correctness of any new driver/adapter logic, resource lifecycle (connections,
   thread pools, executors not being closed/shutdown), thread-safety in producer/consumer paths, and
   whether `driver-api` contracts are honored consistently with the other `driver-*` modules.

4. **If the PR touches `driver-redis`**, check it against the patterns already used by the other
   `driver-*` modules in this same repo (e.g. `driver-kafka`, `driver-pulsar`) for structural consistency
   — config class shape, producer/consumer lifecycle, benchmark-driver interface implementation — since
   there's no `driver-redis`-specific written convention to fall back on beyond "does it look like its
   siblings and honor `driver-api`."

5. **If the PR touches Maven dependencies (including transitive ones pulled in via a driver's own
   client library)**, check whether the version is current and whether it could affect other modules
   sharing the parent POM — this is a multi-module reactor build, so a bump in one driver's dependency
   tree is worth a quick check against the parent `pom.xml` for version conflicts.

6. **Write the review terse and mostly as questions.** With zero behavioral precedent to match, default
   to being direct and specific rather than adopting a tone this fork has never actually demonstrated.
   If the PR is small, well-scoped, and easy to verify by reading, the honest output may be no comment at
   all (`skip_comment: true`) — don't manufacture nitpicks on a clean PR to look thorough.

7. **Land on a plain-prose verdict.** No literal "Verdict:" label, no bolded summary line, no `@`-mention
   of any GitHub username — these rules apply regardless of tone; see the workflow's own critical safety
   rules for why.

## What NOT to do

- Don't claim a "maintainer voice" or attribute a nitpick to a supposed maintainer pattern on this fork —
  there is zero recorded review activity of any kind. See the honesty note above.
- Don't imply this fork has ever enforced any review policy, written or unwritten — it has no
  `CONTRIBUTING.md`/`AGENTS.md` and no PR history to have enforced anything against.
- Don't treat upstream `openmessaging/benchmark`'s own (much larger, active) PR/issue history as if it
  were this fork's history. It isn't — this fork's `redis-performance`-side activity is two direct pushes
  to `master`, nothing more.
- Don't re-flag pure style/formatting issues that `mvn verify` (checkstyle/spotless) already enforces in
  CI — that's real signal this repo does have, unlike some other `redis-performance` forks.
- Don't manufacture a duplicate-approval comment ("LGTM") on a routine PR just to produce output.
- Don't literally `@`-mention any GitHub username, ever, for any reason.
