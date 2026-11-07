---
name: astack-init
description: Set up an astack workspace for a project (its own clone in repo/, worktree, story, and lesson folders), then read the repo and write ASTACK.md (what it ships and to whom, commands, done checks, risk table, evidence kit, systems, boundaries, conventions) for the human to confirm. Run once per project, or to refresh ASTACK.md.
---

# Init

## Workspace

Run from the workspace folder, by convention `<project>.astack/` beside where the human keeps code; it is empty apart from the installed skills. The human's own checkout of the project is never touched. Create:

- `repo/`: `git clone <url> repo`, on the base branch.
- `wt/`, `stories/`, `lessons/`.
- `AGENTS.md`, three lines: this is the astack workspace for `<project>`; read `ASTACK.md`; the code is in `repo/` and every task works in `wt/<id>` (`astack-deliver`, Workspace). Agents read `AGENTS.md` directly; no `CLAUDE.md` copy or link.
- `git init` with a `.gitignore` of `repo/`, `wt/`, `stories/`, `.claude/`, `.agents/`: the workspace tracks `ASTACK.md`, `AGENTS.md`, `lessons/`, and the skills installer's `skills-lock.json`.

## ASTACK.md

Write `ASTACK.md` at the workspace root from `astack-template.md` beside this file. It holds only what an agent can't cheaply work out by itself and needs on every task. Facts only; stay under about 8,000 characters. Cite one source per section, and a commit or PR per risk row; not one per fact.

First decide the **repo kind** (library, app, service, data or config, docs, monorepo of several) and adapt every section to it: rename or replace template rows that don't fit (a data repo has validate and live checks, not build and unit tests) rather than dropping them silently.

## Read `repo/`, in this order, skimming

1. README, CONTRIBUTING, tracked `AGENTS.md` / `CLAUDE.md`, the docs index. Large docs: headings first, then only the sections you need (device matrices and release notes often hide in design docs).
2. Package or build manifests and their scripts; lint, test, and release config.
3. CI workflows: job names, triggers, and `run:` lines only. Note which run on every PR and which only on certain paths, schedules, or releases.
4. Test layout and one representative test; example or demo apps.
5. History: the last ~40 commit subjects; every `fix` / `revert` commit with the files it touched (`git log --oneline | grep -iE ' (fix|revert)'`, then `git show --stat`); 3–5 recent merged PR bodies and their comments (`gh pr list --state merged -L 5 --json number,title,body,mergedBy`, then `gh pr view <n> --comments`), and open PRs that build test harnesses. Fixes and PR bodies are where the risk table comes from.
6. **Consumers** of what the repo ships (apps that use the library, clients that parse the data, services that call the API), and the code on their side that reads it, when it is reachable.
7. **Evidence tooling:** e2e harnesses and their specs, example or demo apps, screenshot or capture scripts, benchmark and profiling scripts, device or simulator scripts, what CI builds and uploads as artifacts, evidence designs in docs (scenarios, thresholds, device matrices: point at them, don't copy), and how past PRs showed evidence (tables, timelines, images, recordings, usually in comments).

When listing files, exclude `node_modules`, `Pods`, build output, and agent worktree folders (`.claude/worktrees`, `worktrees/`). Never open secrets (`.env*`, keystores, key files, credential stores); find them by name only (`git ls-files | grep -iE 'env|key|secret|credential'`, `find . -maxdepth 3 -name '.env*'`) and list them.

## Derive

- **Project:** what it ships, to whom, and in which release units (several published packages, their versions and peer ranges); how a merge reaches users (publish, deploy, CDN purge, store) and any ordering against consumers.
- **Commands:** setup, build or validate, lint, test (all and one), and live or device checks. Prefer what CI runs.
- **Worktree setup:** what a fresh worktree needs before it builds: install, generated files, and local config files, by name, with how they get there (a copy command the human approves; never read their contents).
- **Done means:** checks on every PR; path-triggered or manual checks and when they apply; bookkeeping the repo always does (version bumps, changelog, provenance tables, docs), found from commit history.
- **Risk table:** the most valuable section. For each kind of change the repo has, the evidence that proves it, grounded in what broke before and what CI can't see (devices, real servers, consumers, store review, caches, compatibility with shipped clients). One row each: *change touches* → *evidence it needs* (source).
- **Evidence kit:** the second most valuable section. Name the kinds by what the product outputs: UI gets screenshots and recordings; audio gets audio files, recordings, or signal checks; services get request and response logs; timing gets timelines and traces; plus tests and mutation, e2e, measurements, devices and real systems, CI artifacts. For each, how to produce it in this repo: the command or steps, how to build the base next to the branch on the same device, how to find the specs that drive a surface, the metric, statistic, run count, and thresholds (from the repo's docs and past PRs; none → propose), and where each media type is hosted so a PR shows it (images and video render inline in a GitHub comment; other files are linked). Prove the key ones by running them when that is cheap and safe, in a scratch worktree (`git -C repo worktree add ../wt/init-check`, removed afterwards), never in `repo/`; otherwise record what running takes, and cite a past PR that ran it. Where a kind is missing (a UI with no way to capture it, a perf claim with no benchmark), say so and propose the smallest way to add it, as a question. Risk rows name the kit entries they need.
- **Systems:** real systems the code depends on, how an agent reaches them (simulator, device, sandbox, live), what tests fake, or "live only".
- **Boundaries:** public vs private; files never read; files never touched: generated (confirm with `git log -- <file>`: bot or tool commits only), vendored, submodules, and frozen by policy; who merges and releases (from `mergedBy` and release commits).
- **Conventions:** commit and PR format, PR body shape (from recent PRs when there is no template), branch naming.

## Ask

Only what the repo doesn't answer, in one message with your best guess for each: where to file bugs, the channel for questions, devices or accounts agents may use, and any conflict between the repo's existing practice and the human's own rules (internal links or IDs in public text, commit trailers).

## Write

1. `ASTACK.md` from the template, then commit the workspace.
2. In your message, not in `ASTACK.md`, suggest guard hooks for the boundaries that would be costly to break (secrets, generated or frozen files, pushes to the base branch).

Show the human the file and the open questions. On a refresh, `git -C repo pull` first, diff against the current `ASTACK.md`, and keep the human's edits.
