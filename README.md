# wiscoai-rules

Shared rules for working with AI agents: how sessions carry work over, how they hand over, which model a task runs
on, and how the work itself is done. Plain markdown, one file per topic, written for any project and any agent.
Take them whole or by topic.

| File | What it covers |
|---|---|
| `sessions.md` | the log, the repo as memory, fresh sessions, one writer at a time, commits |
| `handover.md` | the closing line (what next, where, which model), a question or a hand-over, work for another place |
| `models.md` | a model per task, one model per session, subagents only where they pay |
| `working.md` | automate and explain, the CLI first, manual steps with their commands, drift, secrets |

## Use it

Check this repo out beside your project, and import the files you want from the `CLAUDE.md` your sessions start
from (the folder that holds both checkouts):

```
git clone https://github.com/leewiscovitch/wiscoai-rules.git
```

```
@wiscoai-rules/sessions.md
@wiscoai-rules/handover.md
@wiscoai-rules/models.md
@wiscoai-rules/working.md
@<project>/AGENTS.md
```

The files are read from disk when the session starts: no network, and no more tokens than the same text in your
own file. A wrong path fails silently, so ask a new session which instruction files it was given.

## What your project's own file must say

- **The owner's name**: the rules speak of "the owner"
- **The log's path**, if not `docs/log.md`
- **The places work runs**, if more than one (hosts, clones), and which task runs where

## Your project's file wins where the two differ

The rules are a default. Where your project's own file says something else, it wins.

## Updating

Users follow `main`: `git -C wiscoai-rules pull`. A running session keeps what it loaded at its start; the next
session picks up the change.

## Licence

MIT, see `LICENSE`.
