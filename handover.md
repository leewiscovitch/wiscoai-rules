# Hand-overs

How a session ends, and what it does when the work belongs elsewhere.

- **Every hand-over names the model.** The `Next:` line of a log entry and the last line of a session's closing
  message say what comes next, where, and on which model, taken from the task's `Model:` line, e.g.
  `Next block: Task 3 in a fresh session on the build server, model opus.` Work that is in no plan gets its model
  by the list in `models.md`.
- **The closing message ends with that line**, alone in a fenced code block (the owner, named in the project's own
  file, copies it with one shortcut and pastes it as the next session's first message).
- **A question or a hand-over, never both.** A message that asks the owner something is not a closing message: it
  has no hand-over block, and the owner answers in the same session. The block comes only when nothing is open for
  this session. If a decision is still the owner's and the session ends anyway (long work would follow), the
  decision is written into the block as the next session's first step, and the message asks nothing.
- **Work that needs another place.** If a task needs another place (another host, another clone), say so and stop:
  don't work around it. Name the place to use and give the owner a markdown block to paste into that place's
  session: what to do, the exact commands, what to expect, and what to report back.
