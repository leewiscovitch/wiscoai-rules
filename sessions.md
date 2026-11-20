# Sessions

How work carries over between sessions: the log, the commits, one writer.

- **Read the log first.** The project's log (`docs/log.md` unless the project says otherwise) is a running log of
  what was done and what's next. **Read its newest three entries first in every new session** (not the whole
  file: it is long and only grows); search the rest (`grep`) when something older is needed. No log yet: the
  session's closing entry creates it.
- **The repo is the memory** for the work: sessions and agents share nothing else about it (the memory notes,
  `memory.md`, are only for how the owner works). Every work session ends with changes committed and pushed, a
  short entry at the top of the log (done / next), and the docs updated to match.
- **Fresh sessions over long ones.** Start a new session per task; the repo and the log carry the context.
  A session working on one task of a plan reads that task and the plan's status notes, not the whole plan; of the
  big docs, the section it needs.
- **One writer at a time.** Every host has its own clone: pull first, commit and push before another one starts.
- **Before every commit**, `git diff --stat`: only the files you meant to touch. Keep commits small and about
  one thing; say what and why in the message.

## Never

- Rewrite or truncate the log: add a new entry at the top, keep the history
- Force-push, or rewrite git history
