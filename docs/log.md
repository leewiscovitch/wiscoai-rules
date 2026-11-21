# Log

Newest first. Each entry: what was done, what's next (where, which model).

## 2026-11-21: `memory-sync start`: the new index lines, the rules in the background

Done (`wiscolabx` plan `2026-11-20-shared-rules-memory.md` Task 4, Step 6, on `admin`, model opus). Seen live:
Claude Code reads `MEMORY.md` and the imported rules **before** the `SessionStart` hook runs, so a note pulled at
start reached the index one session late, and the rules pull (~0.7 s) only ever helped the next session while
holding up this one (`start` ~1.4 s on a machine that pulls the rules over SSH).
- `start` prints the lines its pull added to `MEMORY.md`, after the header `memory-sync: lines added to MEMORY.md
  since this session loaded it:` (a hook's output reaches the session; checked with a headless session)
- The rules pull runs detached (`setsid -f`, the script again in an internal `rules` mode, every output closed):
  `start` ~0.7 s
- `tests/memory-sync-test`: twelve cases (`new_index`; `rules_slow`, which the old script failed at 4 s;
  `rules_dirty` waits for the background pull). `shellcheck` clean, `tests/check` passes. `README.md`, `AGENTS.md`

Next: back in `wiscolabx`: plan Task 4, Steps 6 and 7 finish in the same session.

## 2026-11-20: `memory.md`, behaviour run 7

Done (`wiscolabx` plan `2026-11-20-shared-rules-memory.md` Task 2, on `admin`, model opus):
- `memory.md`, the fifth rule file: a note is for how the owner works (a correction, a standing preference), one
  fact with its date, who and why; the project's facts and never a secret stay out; the notes are shared through
  git, a conflict is told to the owner; on a review each note is promoted or deleted, with a yes per note
- `sessions.md`: "The repo is the memory **for the work**", the notes only for how the owner works. `handover.md`:
  "**Every** closing message ends with that line ... also when nothing is planned next or the project changes what
  the line holds" (run 5 failed twice without it; the same meaning as `wiscolabx/AGENTS.md`, said harder).
  `tests/check`: five files, at most 1000 words (exactly 1000 now: any addition needs a cut or a new limit).
  `README.md`: the table row, the import, "Sharing the notes between machines" with a settings snippet
- Behaviour run 7 (a correction and a project fact in one prompt) passes; runs 1–6 again: all pass, run 5 after
  the fix (three rounds). Replies in `tests/behaviour.md`

Next: plan Task 3 (the notes repo `wiscoai-memory`, its keys, its first content) in a fresh session on `admin`,
model opus.

## 2026-11-20: the memory sync script

Done (`wiscolabx` plan `2026-11-20-shared-rules-memory.md` Task 1, on `admin`, model opus):
- `hooks/memory-sync <start|end> <memory-dir>`: commits what is in the notes folder, fetches, rebases, pushes;
  `start` then fast-forwards this repo's clone, only when it is clean. Always exits 0; every network step under
  `timeout` (`MEMORY_SYNC_TIMEOUT`, default 5 s, the whole git command); the rebase itself is local, with no timeout,
  so a slow network can't leave it half done; one `flock` per clone (`.git/memory-sync.lock`); a push rejected after
  a good fetch is tried once more. On a conflict: `rebase --abort`, the local commits stay, `start` prints one line
  (`memory-sync: notes out of sync on <host> in <dir>: ...`). A host with no git identity commits as `<user>@<host>`
- `tests/memory-sync-test`: ten cases, offline, the script always run from a copy of this repo cloned from a local
  bare one (so the test never pulls the real clone). `tests/check` now guards `hooks/` for project names too

Next: plan Task 2 (`memory.md`, in this repo) in a fresh session on `admin`, model opus.

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
