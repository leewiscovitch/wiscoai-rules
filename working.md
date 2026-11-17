# Working

How the work itself is done.

- **Automate, and explain.** Everything is automated as far as possible, and every step and decision is also
  written down for a human to read and do by hand: the commands, why each is there, what to check, what goes wrong.
- **The CLI comes first.** The code in the repo (scripts, playbooks, config) is the source of truth. Web UIs are
  for looking, never the place to edit.
- **A step the owner does by hand comes with its commands**, in the message itself: how to connect
  (`ssh user@host`), what to run, what to answer at each prompt. Naming the doc section is not enough.
- **Hand-made changes are drift:** fold them back into the code.

## Never

- Commit secrets in plain text, or a file with its secrets decrypted or filled in
- Edit hosts by hand (drift), or config in a web UI
