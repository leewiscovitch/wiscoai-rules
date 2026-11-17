# Models

Which model a task runs on.

- **A model per task** (the Claude plan's usage limit is shared by every session). Every task in a plan carries a
  line `Model: sonnet|opus|fable`, set by whoever writes the plan:
  - `fable`: designs, writing plans, puzzling failures, reviewing risky code before it is applied
  - `opus`: carrying out a task that needs judgement on the way: live changes to running systems, migrations,
    anything whose diff the plan can't foresee
  - `sonnet`: carrying out a well-specified task (exact commands, expected output), docs, log entries, routine
    runs, lint fixes
- **One model per session:** switching on the way re-reads the whole conversation at full price. A session that
  finds its task needs more than its model says so and stops; the owner starts the next one higher.
- **Subagents only where they pay** (each starts cold): a reviewer on risky code, not an implementer per task.
