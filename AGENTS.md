# wiscoai-rules: instructions for AI agents and humans

The owner is Lee. This repo holds shared rules for working with AI agents (`sessions.md`, `handover.md`,
`models.md`, `working.md`), imported by other projects; `README.md` says how. This repo follows them itself
(`CLAUDE.md` imports them).

- **Read `docs/log.md` first**: its newest three entries. Designs and plans are in `docs/superpowers/`
- **The rule files name no project.** "The owner", not "Lee"; neutral examples; no host names, addresses or tools
  of one project. `tests/check` guards that, the length (four files, at most 800 words) and the key phrases
- **A rule change** is checked with `tests/check` (must exit 0) and read against `wiscolabx/AGENTS.md`, the rules'
  source: the same point must mean the same in both, until that repo is moved onto these files. Never edit
  `wiscolabx` from here
- **Behaviour** is proven by headless sessions in a test project outside `/opt/claude/admin`; the runs and their
  results are in `tests/behaviour.md`
- Users follow `main`: every push reaches them at their next session. Keep `main` working
- Local clone: `/opt/claude/admin/wiscoai-rules` on `admin`; remote `git@github-wiscoai-rules:leewiscovitch/wiscoai-rules.git`
