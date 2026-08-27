# Mined history — redis-performance/openmessaging-benchmark, and why this file is short

Mined on 2026-08-27 using only data scoped to the `redis-performance` fork itself — never upstream
`openmessaging/benchmark`'s own PR/issue history, per this skill's explicit scope (upstream's
contributors and reviewers are not this fork's own maintainers, so their review activity says nothing
about how *this repo* has actually been reviewed).

Commands run:
- `gh pr list --repo redis-performance/openmessaging-benchmark --state all --limit 100` → `[]`
- `gh issue list --repo redis-performance/openmessaging-benchmark --state all --limit 100` →
  `the 'redis-performance/openmessaging-benchmark' repository has disabled issues`
- `gh api repos/redis-performance/openmessaging-benchmark/contents/AGENTS.md` → 404
- `gh api repos/redis-performance/openmessaging-benchmark/contents/CONTRIBUTING.md` → 404
- `gh api repos/redis-performance/openmessaging-benchmark/commits --paginate` (full history, since this
  fork was never re-based/squashed — its `master` is upstream's own commit history plus two more)
- `gh api repos/redis-performance/openmessaging-benchmark/compare/openmessaging:master...redis-performance:master`

## What this fork's own activity actually is

Exactly two commits, both pushed directly to `master` with no PR in between:

| Commit | Date | Message | Author |
|---|---|---|---|
| `854f73d` | 2021-09-24 | "Initial Redis support (#196)" | Filipe Oliveira |
| `f2a4e8c` | 2023-09-07 | "Bumped Jedis to v5.0.0 - fixed breaking changes from v3.7.0 (#390)" | Filipe Oliveira |

The `#196` and `#390` in those messages are **upstream `openmessaging/benchmark` PR numbers** —
carried over from when these changes were first merged into the upstream project — not PRs opened
against `redis-performance/openmessaging-benchmark`. This fork itself has never had a pull request.
Both commits are by the same person, using GitHub's standard commit-message format for a squash-merged
upstream PR; there is no fork-local review trail attached to either one.

Everything else on this fork's `master` branch (hundreds of commits going back years, from
`openmessaging/benchmark`'s own many contributors — Elliot West, Dave Maughan, Matteo Merli, and dozens
of others) is upstream's own history, inherited because this fork was created as a direct branch/fork
rather than a squashed import. None of those commits, reviews, or PRs belong to `redis-performance`, and
none of them describe how *this fork* reviews changes — they describe how the upstream Apache/OpenMessaging
project reviews changes, which is a different (much larger, much more active) governance context.

## The fork is dormant and behind upstream

`git compare openmessaging:master...redis-performance:master` shows the fork's `master` is frozen at
`f2a4e8c` (2023-09-07) while upstream has continued to `5b1fa70` and beyond (including upstream PR #443,
"Add message size distribution support for realistic workload testing", merged 2026-04-07). This fork
has not pulled upstream changes in roughly three years. Whatever review automation is added here should
not be read as evidence of active, ongoing maintenance — it's being added proactively, not because this
fork has a review backlog or active contributor base today.

## No written policy exists

Neither `AGENTS.md` nor `CONTRIBUTING.md` exists in this fork (both 404 against the default branch).
There is no analogue to check for a stated "one approval required" rule, and no analogue to check whether
such a rule has ever actually been enforced — because there has never been a PR for a rule to apply to.

## Honest bottom line

If asked "how would a redis-performance/openmessaging-benchmark maintainer review this," the honest
answer is: there is no data describing how they ever have, because they never have. The only grounded
material available is structural — this is a fork of an active upstream Java/Maven project, kept
specifically for its `driver-redis` module, currently three years stale, with a working `mvn verify`
CI job and no PR/issue process of its own to date. `references/review-checklist.md` builds on exactly
that and nothing more. Do not manufacture a maintainer personality, a quote, or a "this has come up
before" claim for this repository — none of that exists in the record.
