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
| `memory.md` | what a memory note is for (how the owner works), what it is not, sharing notes, promoting them |

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
@wiscoai-rules/memory.md
@<project>/AGENTS.md
```

The files are read from disk when the session starts: no network, and no more tokens than the same text in your
own file. A wrong path fails silently, so ask a new session which instruction files it was given.

## Sharing the notes between machines

Claude Code keeps memory notes in a folder per project on each machine. To give every machine the same notes:

1. Make a **private** repository of your own for them (the notes are about you; never put one in a public repo),
   and clone it on each machine, e.g. to `/home/you/memory`. Each machine needs push access to it.
2. Point Claude Code at the clone and run `hooks/memory-sync` as two hooks, in a settings file for the machine:
   `~/.claude/settings.json`, or the managed settings file (`/etc/claude-code/managed-settings.json` on Linux).
   `autoMemoryDirectory` is ignored in a project's own checked-in settings. Use absolute paths:

   ```json
   {
     "autoMemoryDirectory": "/home/you/memory",
     "hooks": {
       "SessionStart": [{ "hooks": [{ "type": "command",
         "command": "/home/you/wiscoai-rules/hooks/memory-sync start /home/you/memory" }] }],
       "SessionEnd": [{ "hooks": [{ "type": "command",
         "command": "/home/you/wiscoai-rules/hooks/memory-sync end /home/you/memory" }] }]
     }
   }
   ```

3. Import `memory.md`, so sessions know what belongs in a note.

`start` commits what a session left, pulls and pushes, then fast-forwards this repo's clone if nothing is being
worked on in it; `end` commits, pulls and pushes. Both always exit 0 and give up on the network after
`MEMORY_SYNC_TIMEOUT` seconds (default 5): an offline machine starts as fast as ever and syncs next time. Nothing is
ever reset or force-pushed; if two machines' notes conflict, the local ones stay committed and the next session is
told one line starting `memory-sync: notes out of sync`. Point the setting at the clone only once the clone exists:
Claude Code would make an empty folder there, and the clone would then fail on it. Running sessions keep the folder
they started with. Test: `tests/memory-sync-test`.

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
