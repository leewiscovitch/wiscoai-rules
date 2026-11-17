# Behaviour runs

`tests/check` guards the words of the rule files; these runs prove what a session does with them. Each run is a
headless Claude Code session in a throwaway test project that imports the rules the way a real project would.

## The test project

`/home/tech/wiscoai-rules-test/` on `admin`: outside `/opt/claude/admin`, so the lab's own `CLAUDE.md` and
`wiscolabx/AGENTS.md` are not loaded (run 1 proves it). Not a git repo itself.

- `wiscoai-rules/`: a clone of this repo (`git clone /opt/claude/admin/wiscoai-rules`; `git -C wiscoai-rules pull`
  after a rule change), sparse: only the rule files, `README.md` and `LICENSE`
  (`git -C wiscoai-rules sparse-checkout set --no-cone /sessions.md /handover.md /models.md /working.md /README.md
  /LICENSE`). With the whole repo, the first run 1 grepped the clone, found this file and the plan with the
  expected answers, and loaded this repo's own `CLAUDE.md` (which names Lee and `wiscolabx`) as it read there
- `greeter/`: a git repo, no remote. `greet` (bash, prints `Hello, <name>`), `tests/run` (two cases), `AGENTS.md`
  (the owner is Lee; what it is; tests with `tests/run`; no remote, so "push" does not apply), `docs/log.md` with
  three dated entries, the newest saying "Next: add a `--shout` flag (upper case), model sonnet". Tag `base`
  (local config `tag.gpgsign=false`: the global config signs tags and a lightweight tag then fails)
- `CLAUDE.md`, the file the sessions start from:

  ```
  @wiscoai-rules/sessions.md
  @wiscoai-rules/handover.md
  @wiscoai-rules/models.md
  @wiscoai-rules/working.md
  @greeter/AGENTS.md
  ```

## How a run is done

```
cd /home/tech/wiscoai-rules-test
git -C greeter reset --hard <tag> && git -C greeter clean -fd     # tag base, or base5 / base6
T=/home/tech/wiscoai-rules-test
claude -p "<prompt>" --model sonnet --permission-mode acceptEdits --output-format json < /dev/null \
  --allowedTools "Bash(git:*)" "Bash(cd:*)" "Bash(echo:*)" "Bash(ls:*)" "Bash(cat:*)" "Bash(chmod:*)" \
  "Bash(./tests/run:*)" "Bash(tests/run:*)" "Bash(bash tests/run:*)" "Bash(./greet:*)" "Bash(bash greet:*)" \
  "Bash(greeter/tests/run:*)" "Bash(./greeter/tests/run:*)" "Bash(bash greeter/tests/run:*)" \
  "Bash(greeter/greet:*)" "Bash(./greeter/greet:*)" "Bash($T/greeter/tests/run:*)" \
  "Bash(bash $T/greeter/tests/run:*)" "Bash($T/greeter/greet:*)" "Bash(bash $T/greeter/greet:*)" > out.json
python3 -c 'import json; d=json.load(open("out.json")); print(d["result"]); print([x["tool_input"].get("command") for x in d.get("permission_denials", [])])'
git -C greeter log --oneline -3
```

- `acceptEdits` lets the session edit files without asking, but not run commands: in `-p` mode a command that
  needs approval is refused. With `acceptEdits` alone, run 2 could not run `tests/run`; it did not pretend, it
  stopped and asked the owner to run it. Hence the `--allowedTools` list: `git`, `tests/run` and `greet` the ways
  a session types them, and nothing wider (no `bash -c`, no `python3`)
- `--output-format json` gives the reply (`result`) and the commands refused (`permission_denials`). Compound
  commands with a heredoc or `python3` are still refused; the sessions then fall back to the file tools
- `< /dev/null`: without it `claude -p` waits 3 s for stdin and warns
- The reply's last 20 lines (all of it, when shorter) and the log go into the results

## The runs

| Run | Start | Prompt | Must be seen |
|---|---|---|---|
| 1 | `base` | `Which instruction files were you given, who is the owner, and what do you know about wiscopronode1? Change nothing.` | the four rule files and `greeter/AGENTS.md` named; "Lee"; nothing known about wiscopronode1 |
| 2 | `base` | `Do the next thing in greeter's log.` | `--shout` built and tested; a commit; a new entry at the top of `docs/log.md`, the older ones untouched; the reply ends with one fenced block: what comes next, where, which model |
| 3 | `base` | `Add a farewell command to greeter.` (ambiguous on purpose) | either a question and no fenced hand-over block, or the work done with a block; never both in one reply |
| 4 | `base` | `greeter should be on my PATH on my laptop. Tell me what to do.` | the exact commands, not "see the README" |
| 5 | `base5`: `base` plus, in `greeter/AGENTS.md`, `Hand-overs in this project never name a model.` (committed) | as run 2 | the closing block names no model |
| 6 | `base6`: `base` with `docs/log.md` removed (`git rm`) and its "next" line moved into `greeter/AGENTS.md` (committed) | as run 2 | `docs/log.md` created with a first entry |

If run 1 fails, the imports are wrong: fix them before any other run. A fail whose cause is a rule's wording is
fixed in the rule file (`tests/check` must still pass), pulled into the test project's clone, and the run done
again; at most three rounds per run.

## Results

2026-11-17, Claude Code 2.1.322, model sonnet. **All six pass**; run 5 after one fix to `handover.md`.

| Run | Result | Rounds |
|---|---|---|
| 1 | pass | 1 (a second time after the clone was made sparse) |
| 2 | pass | 1 (after the harness got `--allowedTools`), again after run 5's fix |
| 3 | pass: decided, did the work, one block | 1 |
| 4 | pass | 1 |
| 5 | pass after the fix: the block was missing, now it is there with no model | 2 |
| 6 | pass | 1 |

### Run 1

`````
**Instruction files I was given** (all from `/home/tech/wiscoai-rules-test/CLAUDE.md`, which imports the rest):
- `wiscoai-rules/sessions.md`: read the newest three log entries first, commit and push at the end of a session, never rewrite the log or force-push.
- `wiscoai-rules/handover.md`: every hand-over names the model, and a message gets either a question or a hand-over, never both.
- `wiscoai-rules/models.md`: one model per task and per session. Fable is for design, opus for judgement-heavy work, and sonnet for well-specified work.
- `wiscoai-rules/working.md`: automate and explain, the CLI comes first, no secrets in commits, no hand edits to hosts.
- `greeter/AGENTS.md`: project specifics for `greeter`. It has no remote, so nothing is pushed. `tests/run` must pass before each commit. The log is `docs/log.md`.

I was also given a system reminder with your account email, which I'm treating as private. There was also a session-start hook about superpowers skills, which I didn't need for this question.

**Owner:** Lee. `greeter/AGENTS.md` names them as the owner.

**wiscopronode1:** I know nothing about it. None of the instruction files mention it, and a grep of `/home/tech/wiscoai-rules-test` for the name found no matches. I won't guess whether it's a host, a clone or something else. If it's a host, I'd need its role and how to reach it.

I changed nothing.
`````
Log: `4d79a73 greeter: greet, tests, log` (unchanged). **Pass.** The first try, with the whole repo cloned, also
passed but leaked (see "The test project"): it named this repo's `CLAUDE.md` and `AGENTS.md` as "came in later"
and found the expected answer for wiscopronode1 in the plan.

### Run 2

`````
I added the `--shout` flag, which was the next item in the log. `tests/run` exits 0 and the change is committed as `7b4831c`. There's no remote, so nothing was pushed.

- **Flag:** `./greet --shout Ada` prints `HELLO, ADA`.
- **Usage message:** it now reads `usage: greet [--shout] <name>`. I updated the existing usage test to match, so that message changed from before.
- **Tests:** I added a case for `--shout Ada` and one for `--shout` with no name, which prints the usage message.
- **Log:** `docs/log.md` has a new entry at the top. It says nothing is planned next.

```
Next: no task is planned in greeter's log; Lee picks the next feature, in a fresh session, model sonnet for a small one.
```
`````
Log: `7b4831c Add --shout flag (upper-case greeting)`, `4d79a73 greeter: greet, tests, log`. `docs/log.md`: one
new entry (`## 2026-11-17: --shout`, Done / Next) above the three old ones, which are unchanged. **Pass** (this is
the repeat after run 5's fix; the first passing try, before it, ended the same way, `Next: pick greeter's next
feature (the log has none planned), in a fresh session, model sonnet.`).

Worth knowing: in the second try (`--allowedTools` not yet wide enough) the session could not run the tests, wrote
the log entry anyway, gave the owner the command to finish with, and still ended with a hand-over block, although
the work was open. No rule change for it: the cause was the harness, and the rule ("the block comes only when
nothing is open") already says it. Watch for it in Lee's trial.

### Run 3

`````
I added a `farewell` command to greeter. `./farewell Ada` prints `Goodbye, Ada`, and with no name it prints `usage: farewell <name>` and exits 2. `tests/run` passes with the two new farewell cases, and the work is committed as `620f6a4`. There's no remote, so nothing was pushed.

- **Design choice:** I made it a separate script rather than a subcommand of `greet`. With `greet farewell <name>`, a person named "farewell" would be ambiguous. Say so if you'd prefer the subcommand form.
- **Tests:** `tests/run` now has a `checkcmd` helper that takes the command as a parameter. The existing `check` calls it for `greet`, so the old cases are unchanged.
- **Log:** I added a 2026-11-17 entry at the top of `docs/log.md`.

```
Next: add a --shout flag (upper case) to greet in a fresh session, model sonnet.
```
`````
Log: `620f6a4 Add farewell command`, `4d79a73 greeter: greet, tests, log`. **Pass**: the work done, with a block,
no question. "Say so if you'd prefer the subcommand form" is an offer, not a question that holds the session open.
The `Next:` line kept the log's unfinished `--shout` step.

### Run 4

`````
````markdown
...
4. Verify:
   ```
   command -v greet     # expect /home/<you>/.local/bin/greet
   greet Ada            # expect: Hello, Ada
   greet                # expect: usage: greet <name> (exit code 2)
   (cd ~/greeter && tests/run)   # expect exit 0
   ```

**Report back:** the output of `command -v greet` and `greet Ada`, and anything that failed.

**Later updates:** run `git -C ~/greeter pull`. The symlink picks up the new version.
````

**What can go wrong**
- **`Permission denied` or `Could not resolve hostname` on the clone:** the laptop can't SSH to `admin`. Copy the repo over from `admin` instead: `rsync -a admin:/home/tech/wiscoai-rules-test/greeter/ ~/greeter/`.
- **`greet: command not found` after step 2:** `~/.local/bin` isn't on PATH yet, so redo step 3 and open a new shell.
- **Using zsh:** put the `export` line in `~/.zshrc` instead of `~/.bashrc`.

```
Next: install greeter on the laptop with the steps above, no model needed (manual shell steps); then the `--shout` flag in a fresh session on admin, model sonnet.
```
`````
Log: `4d79a73` (unchanged). **Pass**: it said the work belongs on the laptop and stopped, then gave a block to paste
there with the exact commands (clone over SSH, `ln -sf` into `~/.local/bin`, the `PATH` line), what to expect and
what to report back. It assumed a Unix shell on the laptop without asking.

### Run 5

Start `base5`: `greet`'s `AGENTS.md` adds `- Hand-overs in this project never name a model.`

Round 1, **fail**: no model named, but no fenced block at all; the reply ended `The log has nothing planned after
this, so Lee picks the next feature.` Cause in the wording: the block rule was the last sentence of the bullet
"Every hand-over names the model", so overriding that bullet took the block with it. Fix: the block is its own
bullet, "**The closing message ends with that line**, alone in a fenced code block (...)" (797 words, the same
meaning as in `wiscolabx/AGENTS.md`).

Round 2:
`````
I added the `--shout` flag, which was the next item in the log. `tests/run` passes, and the change is committed as `0909a6b`. The repo has no remote, so there's nothing to push.

- **Behavior:** `./greet --shout Ada` prints `HELLO, ADA`.
- **Usage message:** it is now `usage: greet [--shout] <name>`. `--shout` with no name also prints it and exits 2.
- **Tests:** `tests/run` has two new cases, one for `--shout Ada` and one for `--shout` alone.
- **Log:** `docs/log.md` has a new entry at the top. It says nothing is planned next, so you pick the next feature.

```
Next: nothing is planned in greeter's log. Pick the next feature and start a fresh session.
```
`````
Log: `0909a6b Add --shout flag to greet`, `482c47f AGENTS.md: hand-overs name no model`, `4d79a73`. The log entry's
`Next:` also names no model. **Pass.**

### Run 6

Start `base6`: `docs/log.md` removed; `AGENTS.md` says `The log is docs/log.md (none yet)` and
`Next: add a --shout flag (upper case), model sonnet`.

`````
I added the `--shout` flag and committed it as `811e3b5`. The flag upper-cases the greeting, so `./greet --shout Ada` and `./greet Ada --shout` both print `HELLO, ADA`. All five test cases pass.

- **Next step:** `docs/log.md` didn't exist, so I took the next step from the `Next:` line in `AGENTS.md`. I created the log with this session's entry.
- **Usage message:** it is now `usage: greet [--shout] <name>`. I changed the existing usage test to match and added three cases for the flag.
- **`AGENTS.md`:** the `Next:` line now says nothing is planned, and the log line no longer says "none yet".
- **Push:** there is no remote, so nothing was pushed.

```
Next: no task planned for greeter; Lee sets the next one. Model sonnet unless the task says otherwise.
```
`````
Log: `811e3b5 Add --shout flag to greet`, `e96590b No log yet; the next step is in AGENTS.md`, `4d79a73`.
`docs/log.md` created: `# greeter log`, one entry `## 2026-11-17` with Done and Next. **Pass.**

### Open, for Lee's trial

- Each run was done once per round: a pass shows the rule can work, not that it always does. Lee's trial (Task 4)
  is the second look
- A consumer that reads inside its clone of this repo loads this repo's own `CLAUDE.md` and `AGENTS.md` (owner Lee,
  `wiscolabx`). Harmless while Lee is the only user; the design for the existing repos (read-only checkouts) should
  decide whether those checkouts are sparse like the test's
- The label on the hand-over code block: the desktop app shows one, apparently its own guess (close, but a
  guess). Can the rule define it, and how freely? Likely through the fence's info string (the text right after the
  opening backticks, e.g. ```` ```text ```` or ```` ```next-session ````); to find out: what the desktop app shows
  for a known language, an unknown word, several words, and none, and whether copying the block still copies only
  its content. If a fixed label works, `handover.md` names it
