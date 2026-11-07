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

Each project gets an astack workspace, created by `astack-init` from an empty folder:

```
~/Dev/<project>.astack/        sessions start here
├── ASTACK.md                  project facts (tracked)
├── AGENTS.md, CLAUDE.md       points agents at ASTACK.md (tracked)
├── .claude/skills/astack-*    links to these skills
├── repo/                      the agents' own clone, kept on the base branch
├── wt/<id>/                   one worktree per task
├── stories/<id>/              story.md, evidence.md, evidence/
└── lessons/                   one fact per file (tracked)
```

Your own checkout of the project is never touched. Everything sits under one root, so no harness needs extra folder permissions; `repo/` and the worktrees are siblings, so test runners and bundlers in one never scan another. Nothing is added to the project repo itself.

Start: make the folder, open your agent there, and run `astack-init <clone URL>`. Then review `ASTACK.md` and deliver with `astack-deliver <issue or description>`.

A project that outgrows this (a PM across parallel tasks, shared devices, guard hooks, its own review history) grows the workspace into a full control plane (guards, roles, a PM), as PocketPal's dev team does.
