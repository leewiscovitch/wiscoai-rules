# Working

How the work itself is done.

- **Automate, and explain.** Everything is automated as far as possible, and every step and decision is also
  written down for a human to read and do by hand: the commands, why each is there, what to check, what goes wrong.
- **The CLI comes first.** The code in the repo (scripts, playbooks, config) is the source of truth. Web UIs are
  for looking, never the place to edit.
- **A step the owner does by hand comes with its commands**, in the message itself: how to connect
  (`ssh user@host`), what to run, what to answer at each prompt. Naming the doc section is not enough.
- **Hand-made changes are drift:** fold them back into the code.
- **Never let a command hang the session.** Wrap anything that can wait in `timeout`; use non-interactive flags
  (`ssh -o BatchMode=yes -o ConnectTimeout=10`, `sudo -n`, `-y`). A wait longer than a minute or two (reboot, scrub,
  test run, download) goes in the background (a monitor or a poll loop with a deadline), never a long foreground
  call: the owner's messages wait until the running call ends. A background run that hangs never exits, so look at
  its output. A slow call is never batched with a quick one (results show only when all are done). During long
  work, a short status every 3-5 minutes, even "still downloading, 3 of 5". A step that waits for the owner on
  purpose runs in the foreground, and the session says it is waiting.

## Never

- Commit secrets in plain text, or a file with its secrets decrypted or filled in
- Edit hosts by hand (drift), or config in a web UI
- Kill by name or pattern (`pkill`, `killall`, `pkill -f`): it matches the session itself, or its shell, and ends
  it. Kill only a PID you started and recorded (`$!`), stop a background task with its tool, and start what may need
  killing under `timeout`
