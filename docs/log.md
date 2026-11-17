# Log

Newest first. Each entry: what was done, what's next (where, which model).

## 2026-11-17: a question to discuss, the session-end full run

Done: Lee's question noted in the plan's Task 4 status notes ("Open, to discuss"): keep the full run at the end of
every session, or move it to a nightly CI/CD run and fix what breaks the next day. Nothing decided, no rule changed.

Next: unchanged, plan Task 4 (Lee's trial on `admin`, then a fresh session on `admin`, model sonnet); the question
goes to the next design (model fable).

## 2026-11-17: the behaviour runs

Done (plan Task 3, on `admin`, model opus):
- The test project `/home/tech/wiscoai-rules-test/` (`greeter`, a sparse clone of this repo, a `CLAUDE.md` that
  imports the rules) and `tests/behaviour.md`: six headless runs, how they are done, the replies. All six pass
- `handover.md`: the fenced closing line is its own bullet (run 5: a project that forbade model names lost the
  block with them)
- What is open for the trial is at the end of `tests/behaviour.md` and in the plan's Task 3 status notes

Next: plan Task 4 (Lee tries it; the findings are fixed), Lee's session on `admin` first, then a fresh session on
`admin`, model sonnet.

## 2026-11-17: the rules

Done (plan Task 2, on `admin`, model opus):
- The four rule files at the root (`sessions.md`, `handover.md`, `models.md`, `working.md`), moved from
  `wiscolabx/AGENTS.md` with the wording kept: "Lee" became "the owner", lab examples neutral ones. 797 words
- `tests/check`: the files exist, name nothing of the lab, stay at most 800 words, keep their key phrases
- `README.md` (how a project imports them), `LICENSE` (MIT), this repo's own `AGENTS.md` and `CLAUDE.md`
- What was left out of the source passages, and the one addition, are in the plan's Task 2 status notes

Next: plan Task 3 (the test project and the behaviour runs) in a fresh session on `admin`, model opus.

## 2026-11-16: design, plan, the repo on GitHub

Done: the design and the plan (`docs/superpowers/`); plan Task 1: the GitHub repo (`tofu/github` in `wiscolabx`)
and `admin`'s push key (alias `github-wiscoai-rules`).
