# wiscoai-rules: shared rules for working with AI agents (design)

Approved by Lee on 2026-11-16 (the name and the "new repo first" order are his).

## What it is for

Lee's way of working with agents is written once and reaches every repo of his. Today it lives inside one
project's `AGENTS.md` (`wiscolabx`), mixed with that project's own rules, so a new repo would start with a copy.
Mostly for Lee's own, mostly private, repos; written so that others can take it whole or by topic.

## Goals

- One public repo, `leewiscovitch/wiscoai-rules`, holding the rules as plain markdown, one file per topic
- A project uses it by importing the files it wants beside its own `AGENTS.md`
- Proven in a new test project before any existing repo is touched

## Not in this step

- Changing any existing repo's rules (`wiscolabx/AGENTS.md` stays as it is, the `claude_code` role too). That is
  the next design, after Lee has confirmed this one works
- A Claude Code plugin, a template repo, formats for other agents
- Pinning to a version: users follow `main`

## What would make it a failure

- A session that loads the rules behaves differently from a `wiscolabx` session on the same point (the rules
  are moved, not rewritten)
- A rule that only fits the lab ends up in the shared files (asking for a yes before `tofu apply`, addresses)
- Sessions pay more tokens than the same text costs in `AGENTS.md` today

## Approaches

1. **Shared rules files, imported (picked).** Always loaded, so the end-of-session rules can't be missed; a
   project takes the topics it wants; plain markdown that any agent or person can read. Only reaches sessions
   that have the checkout (or fetch it)
2. **A Claude Code plugin with skills.** Works on any machine, loads on demand. But "end every session with a
   log entry" must be known all the time, and a skill may not fire; Claude Code only. Possible later, in the
   same repo, for step-by-step procedures
3. **A template repo.** Dropped: every repo made from it is a copy that drifts

## The repo

- `leewiscovitch/wiscoai-rules`, public, MIT licence (the licence is the designer's call)
- Written for "the owner", not "Lee": the project's own file names the owner
- Where a project's own file and these rules differ, the project's file wins

| File | Rules (taken from `wiscolabx/AGENTS.md`, wording kept) |
|---|---|
| `sessions.md` | the repo is the memory; read the newest three log entries first; the log entry at the end (done / next); fresh sessions over long ones; one writer at a time; never rewrite or truncate the log; never force-push; small commits about one thing |
| `handover.md` | the closing line says what comes next, where and on which model; it stands alone in a fenced code block; a question or a hand-over, never both; work that needs another place: say so, stop, and give a block to paste there |
| `models.md` | a model per task (`Model:` line in every plan task; what each model is for); one model per session; subagents only where they pay |
| `working.md` | automate, and explain; a step the owner does by hand comes with its commands; the CLI comes first, web UIs are for looking; hand-made changes are drift; no secrets in plain text |
| `README.md` | what this is, how to use it (the import lines), what a project's own file must name: the owner, the log's path if not `docs/log.md`, the places work runs if more than one |

## How a project uses it

The rules are checked out beside the project and imported by the `CLAUDE.md` the session starts from:

```
@wiscoai-rules/sessions.md
@wiscoai-rules/handover.md
@wiscoai-rules/models.md
@wiscoai-rules/working.md
@<project>/AGENTS.md
```

Read from disk at the session's start: no network, and no more tokens than the same text costs today.

## How it is proven (before any existing repo)

A throwaway test project on `admin`, outside `/opt/claude/admin` (so the lab's rules are not loaded): a small
git repo with its own short `AGENTS.md` (owner, what the project is), a `docs/log.md` with a few entries, and a
`CLAUDE.md` with the import lines above. Then:

1. Headless sessions (`claude -p`, model sonnet) with small tasks, each checking one behaviour:
   - reads the newest log entries before working
   - ends with a commit, a new log entry at the top, and the hand-over line alone in a code block, naming the
     model
   - a session that must ask something asks, and gives no hand-over block
   - a manual step for the owner comes with its exact commands
   - a project rule that contradicts a shared rule wins
2. Lee works a real session in the test project and says whether it feels like a `wiscolabx` session
3. What the test shows is fixed in the rules; then the next design (existing repos, the `claude_code` role)

## Creating the repo

GitHub repos of Lee's are managed in `wiscolabx`'s `tofu/github/repos.tf`, so the one touch of an existing repo
in this step is the new `github_repository` resource there and a push key for `admin` (the way the nodes have
one for `wiscomultiprox`). Applied only after Lee has seen the plan and said yes. Until then the repo is local
(`/opt/claude/admin/wiscoai-rules`), which is enough to write and test the rules.

## Left to the builder

- The exact wording of each file (moved from `wiscolabx/AGENTS.md`, "Lee" → "the owner", lab examples replaced
  by neutral ones)
- The test project's place, its tasks and how each check is read
