# astack

A small, portable delivery stack for coding agents: principles and a loop.

| Skill | Use |
| --- | --- |
| `astack-init` | once per repo: reads it and writes `ASTACK.md` (commands, done checks, risk table, boundaries, conventions) |
| `astack-deliver` | one change from request to a review-ready PR, stopping only for real product calls or real blocks |
| `astack-review` | independent review of a diff or PR through lenses, findings refutation-tested |

Everything project-specific lives in the repo's `ASTACK.md`; the skills stay generic.

## Core ideas

- Facts are proven; product calls are the human's, asked once with numbers and a recommended default.
- Reproduce first; compare with the baseline in the same session.
- Evidence closes with receipts.
- A failed attempt is a disproved hypothesis: find the cause, try alternatives, then decide.
- A wrong design goes back; nobody reviews their own work.
- The smallest change that meets the request; only armed follow-ups count.

## Using it in a project

Each project gets an astack workspace beside your code:

```
<project>.astack/              sessions start here
├── ASTACK.md                  project facts (tracked)
├── AGENTS.md                  points agents at ASTACK.md (tracked)
├── skills-lock.json           installed skill versions (tracked)
├── .agents/, .claude/         the installed skills
├── repo/                      the agents' own clone, kept on the base branch
├── wt/<id>/                   one worktree per task
├── stories/<id>/              story.md, evidence.md, evidence/
└── lessons/                   one fact per file (tracked)
```

Your own checkout of the project is never touched, and nothing is added to the project repo. Everything sits under one root, so no harness needs extra folder permissions; `repo/` and the worktrees are siblings, so test runners and bundlers in one never scan another.

## Install and start

```bash
mkdir <project>.astack && cd <project>.astack
npx skills add a-ghorbani/astack -a claude-code codex opencode pi   # project scope: this workspace only
```

Open your agent in that folder and run `astack-init <clone URL>`; review `ASTACK.md` and its questions; then `astack-deliver <issue or description>`. Update the skills with `npx skills update`.

Install per workspace rather than globally (`-g`): globally, astack's skills would compete with the delivery and review skills of projects that have their own workflow.

A project that outgrows this (a PM across parallel tasks, shared devices, guard hooks, its own review history) grows the workspace into a full control plane with guards, roles, and a PM.
