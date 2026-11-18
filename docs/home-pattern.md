# Multi-repo: the home pattern (or a monorepo)

When an app spans several repos (API, UI, workers, infra), a Claude session started
inside one repo sees only that part. Cross-repo changes then need several sessions
and the plan lives in your head. Two fixes:

## Option A: monorepo
All code in one repo. One session, one branch, one PR. Simplest, if you control the
repo layout.

## Option B: home folder (keeps repos separate)
A parent folder that is **not** a git repo, holding the clones side by side, with
its own `CLAUDE.md`. Start Claude in the parent.

```
app-home/                ← start claude here
├── CLAUDE.md            ← workflow agreement + repo map   (scaffold-home/CLAUDE.md)
├── HANDOVER.md          ← session state across all repos
├── docs-claude/         ← specs, build trackers, decisions
├── app-api/             ← its own git repo
│   └── CLAUDE.md        ← technical rules for this repo
├── app-ui/
│   └── CLAUDE.md
└── app-worker/
    └── CLAUDE.md
```

Rules:
- The home `CLAUDE.md` sets the **workflow**; each repo's `CLAUDE.md` sets its
  **technical rules** and wins on technical questions.
- The repo map lists every repo: role, stack, default and dev branch, and whether it
  is infra-only.
- Cross-repo ticket: one plan, one ticket, **one branch and PR per repo** (same
  branch name), and each PR states the merge order.
- Stage per repo; never a blanket add across repos.
- Keep env files out of the repos; canonical copies live in one folder outside them.

## Setting it up for existing repos
1. `mkdir app-home && cd app-home`
2. Clone (or move) the repos inside. Moving local clones is fine; git does not care
   about the parent folder.
3. Copy `scaffold-home/CLAUDE.md`, fill the repo map (Claude can fill it by reading
   each repo's README, package files and `git branch -a`).
4. Add `HANDOVER.md` and `docs-claude/`.
5. Optional: make `app-home` itself a small private git repo to version the working
   docs. Add the sub-repo folders to its `.gitignore`.
