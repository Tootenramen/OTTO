[![OTTO](docs/hero.png)](https://tootenramen.github.io/OTTO/)

# OTTO - a universal project automation suite

OTTO runs unattended AI development sessions on any project, under rules, locks, a guard, a supervisor and a kill
switch, and makes every session **prove its work** instead of claiming it. **Mission Control** is its command center.

> This repository is the **overview and architecture write-up**. The source code is not published here.
> Live site: https://tootenramen.github.io/OTTO/ · More projects: https://github.com/Tootenramen

## Origin

OTTO was not designed on a whiteboard. It was extracted from the automation behind an **agent-built Unreal Engine
game** - about 109k lines of code in 26 days. The factory that ran those sessions, called **Cecilia**, worked for
days at a time with no end in sight, and **Mission Control** was built to watch her run. Every safety feature below
exists because long unattended runs need it. The engine, game and project names were then removed so OTTO runs on any
project, not only a game.

## How it works

1. **A rules file the agents obey.** Decide-don't-ask, six ask-first items, stop conditions, proof or it didn't happen.
2. **Lanes.** READ unlimited, CODE many (each on its own checkout), ASSET exactly 1, BUILD exactly 1.
3. **A structural guard.** A hook outside the agent's control enforces category ownership, own-checkout-before-write,
   lock discipline and bounded delegation, and has its own regression suite.
4. **A handoff loop.** A session works to about 80% of its context, verifies to about 95%, rewrites its own handoff
   prompt, and restarts fresh.
5. **A kill switch.** A single marker file is the machine's off switch. Only the control command removes it, and the
   supervisor restores it if a worker deletes it. The supervisor relaunches dead drivers with backoff, quarantines a
   lane after repeated deaths, and never kills anything.
6. **A proof gate.** One verification command, one verdict, one evidence folder.
7. **A profile.** One file tells OTTO what *your* project launches and builds (launch threshold, gated scripts and
   patterns, build tools, watched processes). Wrong values are ignored and logged, never raised.
8. **Cost tracking.** One CSV row per finished session. OTTO ships no prices and will not invent them.

## Directory structure

```
OTTO/
  otto_init.ps1        deploys OTTO into a project, leaves the machine OFF, runs the proof suite
  core/                the control plane: control command, loop driver, supervisor, governor, rotation,
                       report, cost, live view, terminal dashboard, default lanes / profile / pricing
  hooks/               the guard (ownership, checkout, lock, delegation), stop hook (context gate + handoff),
                       pre-tool hook (launch gate), subagent hook, shared profile loader, hook wiring
  locks/               per-path checkouts (subtree conflicts both ways) and global locks
  tests/               the 383-decision selfcheck and the 53-check supervisor test, with fixtures
  agents/              implementer and scout subagent definitions
  profiles/            worked examples: node-web, python-service
  dashboard/           Mission Control: Node 24 server + React app, its own tests and docs
  dashboard-lite/      zero-dependency, read-only, localhost-only view in one Node file and one HTML page
  docs/                the ten rules, an orientation template, the supervised-operation reference
  examples/            the handoff-prompt template
  memory/              the lessons, written down: session loop, supervised operation, autonomy, context budgeting
  provenance/          how the tool was sanitized and ported, with test evidence
```

## Mission Control

The home page is the **factory board**: machine state, a four-tile strip (tasks done, workers up, gates green, what
waits on you), one character card per lane, and a feed with Waiting, Trouble, Proof and Cost tabs. Other views:
Otto (who is doing what), Live Activity, Decisions, Journal, Board, Roadmap, Bible & Knowledge, Git & Changes,
Verification, Agents, Artifacts, JARVIS (assistant and spoken briefing), Requirements and Settings.

It is read-only by default. Every write to the project is a gated action with a diff, lock checks and a commit. Each
project gets its own stable port in 4720-4799 and its own data directory.

## Verified

| Check | Result |
|---|---|
| Hook decisions (guard, stop, pretool, subagent, profile, cost) | 383 / 383 |
| Supervisor recovery (relaunch, backoff, quarantine, markers, usage-limit waits) | 53 / 53 |
| Deploy into empty folders; dry run writes nothing; proof passes in the target | verified |
| Mission Control in a freshly deployed project: type-check, build, tests | 43 / 43 |
| Database upgrade on an existing database, with an automatic backup | verified |
| A week of unattended operation of this exact tree | **not done** |

## Known gaps (honest)

- Never run unattended for a week. The first real run should be one lane, supervised, for a few hours.
- No container isolation. Workers run with permissions bypassed on the host, protected by the hooks.
- Windows-first. The control plane is PowerShell and bash; a POSIX port is the largest missing piece.
- The `claude -p` CLI is the only worker runtime.
- Task intake is one markdown backlog, not a ticket board.
- No agent-quality baseline (error rate, fix-commit ratio) beyond cost.
