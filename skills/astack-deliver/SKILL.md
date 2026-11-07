---
name: astack-deliver
description: Deliver one change from request to a review-ready PR with evidence receipts and independent review, stopping only for real product calls or real blocks. Use inside an astack workspace (a folder with ASTACK.md) for "deliver / fix / implement <issue or description>"; a repo with its own delivery workflow uses that instead.
---

# Deliver

You own one change from request to a review-ready PR. You orchestrate and build; fresh subagents critique and review. You run from the project's astack workspace; project facts (commands, risk table, evidence kit, boundaries) are in its `ASTACK.md`. If there is no workspace, run `astack-init` first.

## Principles

1. **Facts are proven; product calls are the human's.** A premise is read from code (`file:line`) or proven by a run or spike with its receipt. User-visible behaviour the request leaves open, and accepting a regression or limitation, are product calls: you recommend, the human decides.
2. **Reproduce before fixing.** A bug is red on the base branch before any code, with its cause found (`file:line`; for a regression, the change that introduced it). A feature has a done check that can fail.
3. **Evidence has receipts, planned before code.** Every row of `evidence.md` closes with something a reader can open: command and output, test name, capture, trace, log. "Should work" and "unit tests pass" are not receipts for behaviour a user sees. A change a user can see or hear carries captured output.
4. **Compare with the baseline, in the same session.** A perf, flakiness, or device claim compares the branch with the base, interleaved, with enough runs that the gap exceeds the spread. Yesterday's number is not a baseline; one run decides nothing.
5. **A failed attempt is a disproved hypothesis, not a stop.** Find why (profile, trace, bisect, log), size it against the base, then try two or three alternatives against the cause, measured the same way, and keep the best with its numbers. A tool that hits a limit (disk, time, trace size) gets a lighter instrument before you call it blocked. Present on the base at the same size and not what the request is about: a pre-existing limitation; file it and move on.
6. **A wrong design goes back.** When a premise turns out false, or a fix needs a contract, state, or owner the design doesn't have, redo the design instead of patching around it. A second failed fix on the same cause means redesign.
7. **Nobody reviews their own work.** Critique and review run in fresh subagents (the harness's subagent tool, else a fresh session) with self-contained prompts: the role, absolute paths to the artifacts, what to return, and "do not edit files"; never your reasoning.
8. **The smallest change that meets the request.** Every added line has a reader and traces to the request. Remove what the change makes dead. No speculative guards, no flags nobody asked for. Default to no comments (no comments to justify workarounds and shortcuts); keep only a "why" the code can't show.
9. **Only armed follow-ups count.** A bug found out of scope is filed where `ASTACK.md` says, with repro and receipt. A list in a reply arms nothing; nor does "I'll check when CI finishes" without a watcher that outlives you.
10. **State lives in files.** `story.md` and `evidence.md` are the record. After context loss, re-read them; don't trust memory.
11. **Respect the boundaries.** `ASTACK.md` names what is public, what is never read (secrets), and what is never touched. Public text references only public things.

## Size

- **quick:** one place, an obvious fix, no new contract or state.
- **standard:** new state, contract, or behaviour across modules; adds design and critique.
- **complex:** cross-cutting, a new subsystem, a migration, native or build changes; adds research and two independent designs.

A bug is sized after its cause is found.

## Workspace

The session starts in the workspace: `ASTACK.md`; `repo/`, the agents' clone, kept on the base branch (read it, never edit or build in it); `wt/<id>/`, one worktree per task; `stories/<id>/`; `lessons/`.

Start a task with `git -C repo fetch origin`, then `git -C repo worktree add -b <branch> ../wt/<id> origin/<base>`, then the worktree setup `ASTACK.md` names. All edits, builds, and tests happen in `wt/<id>`. The repo's own `AGENTS.md`, `CLAUDE.md`, and CONTRIBUTING still apply. Remove it only after its PR merges or the human says so.

## Story files

`stories/<id>/`: `story.md` (request, cause, decisions, design, progress) and `evidence.md` (the plan and its receipts), from the templates beside this file, with captures and logs under `evidence/`. `<id>`: the ticket reference or `YYYYMMDD-<slug>`. Terse: for the next agent, not a report.

## Loop

1. **Understand.** Read the request, `ASTACK.md`, the code it touches, and the project's lessons. Write the Request (verbatim) in `story.md`, then plan `evidence.md` (below). Sort every open point: observable → evidence row or spike; product call → a Decisions row with your recommended default, marked *shapes contract* when the design depends on it; an unclear request → settle it from the code and history; only what the requester alone can say is a product call.
2. **Reproduce** (bug): the claim row red on the base, with the cause. **Research** (complex, or a contract-shaping call): what users need and how comparable products solve it; it grounds your defaults.
3. **Design** (standard and complex): the contract delta: what changes, the single owner of each new state, invariants, scenarios including failure paths, and what goes away. Every premise proven. Add an evidence row per scenario. Complex: two subagents design independently; choose with reasons, logged.
4. **Critique** (standard and complex): a fresh subagent gets the story, design, and evidence paths only, and checks that premises are proven, every new piece has a reader, no agent made a product call, and every failure scenario has an evidence row. At most two revision rounds; leftover concerns are listed, not blocking.
5. **Build** in small commits, each passing the project's checks. Standard and complex: the thinnest end-to-end slice first, then fill in. Work in `wt/<id>`, never on the base. A surface the plan didn't foresee gets a row.
6. **Verify.** Run every row with the `ASTACK.md` evidence kit and record its receipt. Tests must be able to fail: for each load-bearing line, break it, see a test fail, restore it. A row naming a real system closes only on that system. A failing row → principle 5.
7. **Trim.** Read your own diff against the request; delete what has no reader.
8. **Evidence gate.** Every pre-PR row is `PASS`, `ON BASE`, `BLOCKED` with the lighter attempt, or `dropped` with a reason; a row a matched risk row requires is never dropped. Open each receipt: it must show what the row claims. A gap goes back to step 6.
9. **Independent review.** Run `astack-review` in a fresh subagent with the diff, `story.md`, and `evidence.md`. Fix BLOCKERs and CONCERNs, re-verify the rows the fixes touch, then re-review; after two rounds, stop with the disagreement and both positions.
10. **PR.** Push and open a PR per `ASTACK.md` (draft unless it says otherwise): a few lines on what changed and why. Post the **evidence comment**: each row's claim and receipt, captures inline (before | after), public references only. Run the post-PR rows on the PR's CI build and append their receipts. Never merge; the human does.

## Evidence

Plan `evidence.md` in step 1, from the claim, not the code. Use the repo's evidence design (scenarios, thresholds, devices) when it has one:

- **The claim.** A bug: the reporter's symptom in their words and numbers, red on the base and green on the branch. A feature: the done check in the requester's words, able to fail.
- **Where.** Platforms, and real systems versus fakes.
- **Risk rows.** Every `ASTACK.md` risk row the change matches, with its evidence as written there.
- **Consumers.** Each consumer or release unit the change reaches, and how it is shown to still work.
- **Observable output.** What the user sees or hears, or the system does, in the kit's form (screenshots or recordings, audio, timelines, system dumps). A change: a before and after pair per surface (base build and branch build, same state, same device); a new feature: one per state; a one-line caption each. Hosted where the kit says, so the PR shows it, never as local paths only.
- **Existing e2e.** Specs that already drive a touched surface run, and stay green.
- **Measurements.** A perf, latency, or resource claim, or a hot-path change: base build against branch build on one device in one session (principle 4), with the kit's metric, statistic, run count, and thresholds.
- **When.** Pre-PR, or post-PR when it needs the PR's CI build.

Each row names its exact command, spec, or device steps. Fixtures come from the real system, never hand-written. When the kit can't produce a row (no capture tool, no device), do the lightest honest version, mark what it can't show, and file the missing capability as a follow-up.

## Asking the human

The human is asked product calls and nothing else. Everything the code, the history, a run, or your judgment can settle is yours (which approach, who does the work, adopt or rewrite, fold or follow up): decide, log it as a ruling, and go on. If you would offer options with one marked recommended, you have already decided. A real block (access, credentials, hardware) is reported, not asked.

Send one message with every open question, each with its options, the numbers that matter (size against the base, who is affected), your recommended default, and what you do meanwhile. Use the channel `ASTACK.md` names, else chat. Contract-shaping calls wait for the answer; the rest proceed on their defaults. Log each answer in the story as a ruling.

**Before you stop**, sort each open item: a fact gets proven, an engineering choice gets decided and logged, a found bug gets filed, a product call gets its numbers and default. Stop only when what is left is a contract-shaping product call or a real block (access, credentials, hardware you can't reach after a lighter attempt).

## Finish

Report in a few lines: the PR, the evidence comment's link, rulings still needed, follow-ups filed. A trap a future task would rediscover (a tool quirk, a false premise, a ruling that applies again) becomes one lesson in `lessons/`.
