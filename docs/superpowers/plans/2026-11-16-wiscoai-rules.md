# wiscoai-rules Implementation Plan

> **For agentic workers:** one task per fresh session on `admin`, on the model its `Model:` line names (the
> owner's rule; no subagent per task). Steps use checkbox (`- [ ]`) syntax for tracking. Write what happened
> under the task's "Status notes" before the session ends.

**Goal:** A public repo of plain markdown rules for working with AI agents, proven in a throwaway test project
before any existing repo uses it.

**Architecture:** Four topic files, moved out of `wiscolabx/AGENTS.md` with the wording kept. A project checks
the repo out beside itself and imports the files from the `CLAUDE.md` its sessions start from. A shell check
guards the files; headless Claude sessions in a test project prove the behaviour.

**Tech Stack:** markdown, bash, git, Claude Code (`claude -p`), OpenTofu (`tofu/github` in `wiscolabx`).

**Spec:** `docs/superpowers/specs/2026-11-16-wiscoai-rules-design.md`

## Global Constraints

- Repo: `leewiscovitch/wiscoai-rules`, public, MIT licence. Local clone: `/opt/claude/admin/wiscoai-rules`
- Rule files: `sessions.md`, `handover.md`, `models.md`, `working.md`, at the repo's root
- The rules are moved, not rewritten: the source is `/opt/claude/admin/wiscolabx/AGENTS.md` (read it, never
  edit it). "Lee" becomes "the owner"; a lab example becomes a neutral one
- Nothing lab-specific in the rule files: no `Lee`, `wiscolabx`, `wisco*` host names, `10.42.`, `192.168.`,
  `tofu`, `ansible`, `Proxmox`, `admin`
- The four rule files together: at most 800 words (the source passages are about 745)
- No existing repo's rules change: `wiscolabx/AGENTS.md` and role `claude_code` stay as they are. The only
  `wiscolabx` change is Task 1's (the GitHub repo and `admin`'s push key)
- `tofu/run github apply` only after Lee has seen that exact plan and said yes
- Another session may be writing in `wiscolabx`: before Task 1 touches it, `git -C /opt/claude/admin/wiscolabx
  status -sb` must show a clean tree, level with `origin/main`. If not, stop and tell Lee

## Review Focus

- **An import that does not load.** A wrong path in `CLAUDE.md` fails silently and the session runs without the
  rules. Task 3's first run asks the session to name the rule files it was given
- **The lab's rules leaking into the test.** A test project under `/opt/claude/admin` would also load
  `wiscolabx/AGENTS.md` and every check would pass for the wrong reason. Task 3's first run must not know
  "wiscopronode"
- **A rule that names something the project lacks.** A project with no `docs/log.md` yet: the session creates it
  with the first entry instead of failing or skipping. Task 3, run 6
- **The project's file must win.** A project that says "no model names in hand-overs" gets none. Task 3, run 5
- **A question and a hand-over in one message.** Task 3, run 3

---

### Task 1: The repo on GitHub, and `admin` can push to it

Model: opus

**Files (in `/opt/claude/admin/wiscolabx`):**
- Modify: `tofu/github/repos.tf` (a second `github_repository`, `depends_on` of the deploy keys)
- Modify: `ansible/group_vars/all/main.yml` (`labx_repo_keys.wiscoai-rules`)
- Modify: `ansible/host_vars/admin.yml` (`base_repo_keys`)
- Modify: `docs/reference.md` ("SSH keys", the repo list), `docs/log.md` (a new entry at the top)

**Interfaces:**
- Produces: remote `git@github-wiscoai-rules:leewiscovitch/wiscoai-rules.git`, writable from `admin` as `tech`

- [ ] **Step 1:** Check the `wiscolabx` tree is clean and level (Global Constraints). Pull
- [ ] **Step 2:** Read how `wiscomultiprox` is done and do the same: `tofu/github/repos.tf`, role `base`
  (`base_repo_keys`, `templates/ssh_config.j2`, `tasks/main.yml:25-40`), `docs/reference.md` "SSH keys" (how a
  second key is made and put in the vault)
- [ ] **Step 3:** Make `admin`'s second key the documented way (`~tech/.ssh/id_ed25519_wiscoai-rules`, private
  half in the vault with `vault-add`), public half into `labx_repo_keys` as
  `wiscoai-rules: { tech@admin: "ssh-ed25519 ..." }`, and `base_repo_keys: [wiscoai-rules]` for `admin`
- [ ] **Step 4:** `repos.tf`: resource `github_repository.wiscoai_rules`, `name = "wiscoai-rules"`,
  `visibility = "public"`, `description = "Shared rules for working with AI agents: sessions, hand-overs, a
  model per task"`, `has_issues = true`, `has_projects = false`, `has_wiki = false`, `prevent_destroy = true`;
  add it to the deploy keys' `depends_on`
- [ ] **Step 5:** `cd ansible && ansible-inventory --list > /dev/null`, then
  `ANSIBLE_DISPLAY_OK_HOSTS=false ANSIBLE_DISPLAY_SKIPPED_HOSTS=false ansible-playbook site.yml --check --diff
  --tags base --limit admin`. Expected: the key file and the `Host github-wiscoai-rules` block, nothing else.
  Apply it; a second run shows `changed=0`. `ansible-lint` passes
- [ ] **Step 6:** `tofu/run github plan -out github.tfplan`. Expected: 2 to add (the repo, one deploy key),
  0 to change, 0 to destroy. **Show Lee the plan and wait for his yes**, then
  `tofu/run github apply github.tfplan`
- [ ] **Step 7:** In `/opt/claude/admin/wiscoai-rules`:
  `git remote add origin git@github-wiscoai-rules:leewiscovitch/wiscoai-rules.git && git push -u origin main`.
  Expected: the design and this plan are on GitHub
- [ ] **Step 8:** `docs/reference.md` and a `docs/log.md` entry in `wiscolabx`; commit and push both repos

**Status notes:**

---

### Task 2: The rules

Model: opus

**Files (in `/opt/claude/admin/wiscoai-rules`):**
- Create: `tests/check` (bash, executable)
- Create: `sessions.md`, `handover.md`, `models.md`, `working.md`, `README.md`, `LICENSE` (MIT, "Lee Wiscovitch")
- Create: `AGENTS.md`, `CLAUDE.md` (`@AGENTS.md` and the four imports), `docs/log.md`

**Interfaces:**
- Produces: the four rule files at the root; `tests/check` exits 0 when they hold

- [ ] **Step 1: Write `tests/check`** (fails now: no files). It exits non-zero, naming each miss, unless:
  - all four rule files, `README.md` and `LICENSE` exist
  - no rule file matches (case-sensitive, whole word where it is a word):
    `Lee|wiscolabx|wisco[a-z0-9]+|10\.42\.|192\.168\.|tofu|[Aa]nsible|Proxmox|\badmin\b`
  - `cat` of the four rule files is at most 800 words
  - each phrase below is in its file (`grep -qiF`):

  | File | Phrases |
  |---|---|
  | `sessions.md` | `The repo is the memory`, `newest three entries`, `Fresh sessions over long ones`, `One writer at a time`, `new entry at the top`, `Force-push` |
  | `handover.md` | `Every hand-over names the model`, `fenced code block`, `A question or a hand-over, never both`, `say so and stop` |
  | `models.md` | `A model per task`, `Model: sonnet\|opus\|fable`, `One model per session`, `only where they pay` |
  | `working.md` | `Automate, and explain`, `comes with its commands`, `The CLI comes first`, `drift`, `secrets in plain text` |

- [ ] **Step 2:** Run `tests/check`. Expected: non-zero, every file named as missing
- [ ] **Step 3: Write the four rule files.** Each starts with a `#` title and one line saying what the topic
  covers. Source passages in `wiscolabx/AGENTS.md`:
  - `sessions.md`: "Where things are" (the `docs/log.md` line only); "Working rules": the repo is the memory,
    fresh sessions over long ones, one writer at a time; "Before every commit": the last line (small commits)
    and `git diff --stat`; "Never": the log and the force-push lines
  - `handover.md`: "Working rules": every hand-over names the model, a question or a hand-over never both;
    "Where to run what": the last paragraph (a task that needs the other place)
  - `models.md`: "Working rules": a model per task (the three models and what each is for), one model per
    session, subagents only where they pay
  - `working.md`: "Working rules": automate and explain, the CLI comes first, a step done by hand comes with its
    commands, hand-made changes are drift; "Never": secrets in plain text, config in a web UI

  The log's path is "the project's log (`docs/log.md` unless the project says otherwise)". The owner is "the
  owner (named in the project's own file)"
- [ ] **Step 4: Write `README.md`:** what this is (three lines); "Use it": check it out beside the project, the
  five import lines from the spec; "What your project's own file must say": the owner's name, the log's path if
  not `docs/log.md`, the places work runs if more than one; "Your project's file wins where the two differ";
  "Updating": users follow `main`, a running session keeps what it loaded
- [ ] **Step 5: Write this repo's own `AGENTS.md`** (at most 25 lines): the owner is Lee; what the repo is; a
  rule change is checked with `tests/check` and against `wiscolabx/AGENTS.md` until that repo is moved onto
  these files; `docs/log.md` with its first entry
- [ ] **Step 6:** Run `tests/check`. Expected: exit 0
- [ ] **Step 7:** Read the four files against the source once more, rule by rule: every rule of the listed
  passages is in exactly one file, none is changed in meaning. List what was left out, and why, in the status
  notes
- [ ] **Step 8:** Commit (`The rules: sessions, hand-overs, models, working`), push

**Status notes:**

---

### Task 3: The test project and the behaviour runs

Model: opus

**Files:**
- Create: `/home/tech/wiscoai-rules-test/` (not a git repo itself; outside `/opt/claude/admin`)
  - `wiscoai-rules/`: `git clone /opt/claude/admin/wiscoai-rules`
  - `greeter/`: a git repo: `greet` (bash, prints `Hello, <name>`), `tests/run` (two cases), `AGENTS.md`
    (the owner is Lee; what it is; tests with `tests/run`; no remote, so "push" does not apply), `docs/log.md`
    with three dated entries, the newest saying "Next: add a `--shout` flag (upper case), model sonnet"
  - `CLAUDE.md`: the four `@wiscoai-rules/...` lines, then `@greeter/AGENTS.md`
- Create: `/opt/claude/admin/wiscoai-rules/tests/behaviour.md` (the runs, what each must show, the results)

**Interfaces:**
- Consumes: the four rule files (Task 2)
- Produces: `tests/behaviour.md` with a pass or fail per run; a tag `base` in `greeter` to reset to

- [ ] **Step 1:** Build the test project; `git -C greeter tag base`. Check `claude --help` for the flags below
- [ ] **Step 2:** Write `tests/behaviour.md` first: the six runs below, each with its prompt and what must be
  seen. Each run: `cd /home/tech/wiscoai-rules-test && claude -p "<prompt>" --model sonnet --permission-mode
  acceptEdits`; before each, `git -C greeter reset --hard base && git -C greeter clean -fd`

  | Run | Prompt | Must be seen |
  |---|---|---|
  | 1 | `Which instruction files were you given, who is the owner, and what do you know about wiscopronode1? Change nothing.` | the four rule files and `greeter/AGENTS.md` named; "Lee"; nothing known about wiscopronode1 |
  | 2 | `Do the next thing in greeter's log.` | `--shout` built and tested; a commit; a new entry at the top of `docs/log.md`, the older ones untouched; the reply ends with one fenced block: what comes next, where, which model |
  | 3 | `Add a farewell command to greeter.` (ambiguous on purpose) | either a question and no fenced hand-over block, or the work done with a block; never both in one reply |
  | 4 | `greeter should be on my PATH on my laptop. Tell me what to do.` | the exact commands, not "see the README" |
  | 5 | as run 2, after adding to `greeter/AGENTS.md` (and committing, tag `base5`): `Hand-overs in this project never name a model.` | the closing block names no model |
  | 6 | as run 2, after `git rm docs/log.md` and moving the "next" line into `AGENTS.md` (tag `base6`) | `docs/log.md` created with a first entry |

- [ ] **Step 3:** Run them in order; paste each reply's last 20 lines and `git -C greeter log --oneline -3` into
  `tests/behaviour.md` with pass or fail. If run 1 fails, stop: fix the imports first
- [ ] **Step 4:** For each fail: is the rule's wording the cause? Fix the rule file (`tests/check` must still
  pass), pull it into the test project's clone, run that run again. At most three rounds per run; what still
  fails goes to Lee in the status notes with the replies
- [ ] **Step 5:** Commit `tests/behaviour.md` and any rule fixes, push. Log entry

**Status notes:**

---

### Task 4: Lee tries it; the findings are fixed

Model: sonnet

**Files:**
- Modify: rule files as the trial shows; `tests/behaviour.md` (Lee's findings); `docs/log.md`

- [ ] **Step 1: Lee, by hand** (laptop on the VPN or on the switch's `ether1`; a normal prompt):

  ```
  ssh tech@10.42.10.201
  cd ~/wiscoai-rules-test
  git -C greeter reset --hard base
  claude
  ```

  Then work as in any `wiscolabx` session: ask for a small change to `greeter`, ask a question half-way, let it
  finish. What to look for: does it read the log first, end with the log entry and the hand-over block, ask
  without a block. `/exit` ends it. Report what felt different from a `wiscolabx` session
- [ ] **Step 2:** For each finding: change the rule file, `tests/check`, and the matching run of
  `tests/behaviour.md` again. Commit, push
- [ ] **Step 3:** Log entry: confirmed or not, and what is open. If confirmed, the next step is a new design
  (model fable): the existing repos and role `claude_code` (the import list per repo, read-only checkouts kept
  up to date), as sketched in the conversation of 2026-11-16

**Status notes:**
