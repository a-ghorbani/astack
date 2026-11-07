# ASTACK

Project facts for astack skills (`astack-deliver`, `astack-review`). Facts only; edit freely.

## Project

<kind (library / app / service / data / docs / monorepo); what it ships and to whom>

- Repo: <clone URL> · base branch <name>

- Release units: <package or artifact, version source, peer ranges; one line each>
- Consumers: <who reads what this repo ships, and the code on their side>
- Reaching users: <merge → publish / deploy / CDN purge / store; ordering against consumers>

## Commands

| Purpose | Command |
| --- | --- |
| setup | |
| worktree setup | <install, generated files, local config by name and how it is copied> |
| build or validate | |
| lint / typecheck | |
| test (all / one) | |
| live, device, or example-app checks | |

## Done means

- Every PR: <checks>
- When touched: <path-triggered or manual checks, and their trigger>
- Bookkeeping: <version bumps, changelog, provenance, docs>

## Risk table

| The change touches | It needs | Kit | Source |
| --- | --- | --- | --- |
| <surface> | <evidence: platforms, real system, measurement against base> | <kit rows> | <commit / PR> |

## Evidence kit

| Kind | How in this repo | Verified (or what it takes; past PR) |
| --- | --- | --- |
| tests, mutation check | | |
| e2e (and how to find specs for a surface) | | |
| output captures, base vs branch on one device <rename per product: UI, audio, logs> | <tool, steps, base build> | |
| timing, measurements | <script, metric, statistic, runs, thresholds> | |
| hosting per media type | | |
| devices, real systems | | |
| CI artifacts usable post-PR | | |

Evidence design docs: <paths, or none>

Missing: <kinds this repo can't produce yet; the proposals go in the questions>

## Systems and devices

<real systems and how an agent reaches them; what tests fake, or "live only"; device or account rules>

## Boundaries

- Public: <what is public; public text cites only public refs>
- Never read: <secrets, by name>
- Never touch: <generated, vendored, submodules, frozen by policy>
- Merges and releases: <who>

## Conventions

<commit format; PR title and body shape; branch naming>

## Tracker and questions

- File bugs: <where and how>
- Ask the human: <channel, e.g. Slack or a message bus, else chat>

## Knowledge

- Architecture notes: <path>
- Extra review lenses: <optional>
