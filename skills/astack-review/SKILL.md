---
name: astack-review
description: Independent code review of a diff, branch, or PR in any repo, through explicit lenses, with every finding tied to a concrete failure scenario and refutation-tested before it is kept. Use for "review PR #n / this branch / this diff" and from astack-deliver; a repo with its own review skill uses that instead.
---

# Review

You review code you did not write. If you wrote or designed it in this session, dispatch a fresh subagent with this skill instead. Read the workspace's `ASTACK.md` when there is one: it adds project lenses, risk rows, and posting conventions.

## Inputs

- The diff: a PR number, or `base...head`. Review the whole change, not only the last commit.
- The intent: the issue, PR description, or story file. Without one, infer it from the PR and say so.
- The evidence the author claims, if any.

## Depth

- **Low risk** (small, no new contract or state, no `ASTACK.md` risk row matched): one pass through every lens.
- **High risk** (new state or contract, concurrency, persistence, security surface, native or build, a matched risk row, or a large diff): you lead. Build the map, then dispatch one subagent per relevant lens in parallel, each given the map, the diff range, and its lens; merge and refute their findings yourself.

## Map first

For each changed file: what behaviour changes, who calls or reads it, what state it writes and who else writes that state, which invariants it relies on. Trace each changed function to its callers. Most real bugs sit at those edges, not in the changed lines.

## Lenses

- **Correctness:** edge and empty inputs, error and cancellation paths, ordering and concurrency, state lifecycle (init, teardown, re-entry), resource leaks, off-by-one.
- **Contract and design:** matches the intent; one owner per state; no new contract without a reason; consumers and shipped clients (`ASTACK.md`) still read what they read before; fits existing patterns; nothing left dead.
- **Tests and evidence:** would a test fail if the fix were reverted; assertions on the user-visible result, not a proxy. Open `evidence.md` (or the PR's evidence) and its receipts: each shows what its row claims. A matched `ASTACK.md` risk row with no evidence row, a visible change without before/after captures, or a perf claim without a same-session base measurement is a CONCERN.
- **Security and privacy:** untrusted input at each sink, secrets, permissions, sensitive data in logs.
- **Performance:** hot paths, per-item or per-frame cost, memory, startup. A perf-sensitive change needs a measurement against the base, not reasoning.
- **Simplicity:** every added line has a reader and a reason; speculative guards, unused parameters, needless indirection, comments that restate code.
- **Platform** (multi-platform projects): differences between platforms only.

Add the lenses `ASTACK.md` lists.

## Findings

| Severity | Meaning |
| --- | --- |
| BLOCKER | wrong behaviour, data loss, security hole, broken contract |
| CONCERN | likely bug, untested load-bearing line, unmeasured perf risk, needless complexity |
| SUGGESTION | better, not required; listed as follow-ups, never a fix round |

Each finding has `file:line`, a failure scenario (concrete input or state → wrong result), the evidence (quoted code, a run), and a fix sketch.

**Refute before keeping.** For each BLOCKER and CONCERN, try to disprove it: read the caller, find a guard elsewhere, run the test or a quick repro. Drop what falls. Mark the rest CONFIRMED (traced or ran) or PLAUSIBLE (reasoned, not run).

A finding that needs a new contract, state, or owner the design lacks is a design problem: say so, don't prescribe a patch.

## Verdict and output

`APPROVE` (no BLOCKER or CONCERN) or `REQUEST_CHANGES`.

```markdown
## Review: <PR or range>
Verdict: APPROVE | REQUEST_CHANGES · Depth: low | high (lenses: …)
### Findings
1. [BLOCKER|CONCERN] <file:line>: <one line> · CONFIRMED|PLAUSIBLE
   Scenario: <input/state → wrong result>
   Evidence: <quote or run>
   Fix: <sketch>
### Follow-ups
- <SUGGESTION, one line each>
### Residual risk
<what was not checked and why>
```

Posting: only when asked, as a comment (never an approval or merge on the human's behalf), short, following `ASTACK.md` conventions and its public/private boundary.
