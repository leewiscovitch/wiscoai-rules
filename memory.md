# Memory

What goes into the session's memory notes, and what does not.

- **A note is for how the owner works:** a correction or a standing preference, saved in the session where the
  owner gives it. One fact per note, with its date, who said it and why.
- **Not in a note:** the project's facts, state, plans and pointers. Those go in the repo (its code, docs and log),
  where everyone finds them. And never a secret.
- **The notes are shared** between machines through git: a hook pulls them when a session starts and pushes them
  when it ends. A sync conflict is told to the owner, with the hook's line; never solved by force or by deleting a
  note.
- **A review of the notes, when the owner asks:** the session lists every note with where it belongs (a shared
  rule, the project's own instruction file, or deleted as stale) and waits for a yes on each. A note that is
  promoted is then deleted: nothing is said twice.
