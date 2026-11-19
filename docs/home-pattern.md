# Multi-repo: the home pattern (or a monorepo)

When an app spans several repos (API, UI, workers, infra), a Claude session started
inside one repo sees only that part. Cross-repo changes then need several sessions
and the plan lives in your head. Two fixes:

## Option A: monorepo
All code in one repo. One session, one branch, one PR. Simplest, if you control the
repo layout.

## Option B: home folder (keeps repos separate)
A personal parent folder holding the clones side by side, with its own short
`CLAUDE.md`. Start Claude in the parent.

```
app-home/                ← start claude here (personal; local-only git)
├── CLAUDE.md            ← repo map, cross-repo rules, routing   (scaffold-home/CLAUDE.md)
├── HANDOVER.md          ← the only session state, covering all repos
├── docs-claude/         ← cross-repo specs and decisions
├── .gitignore           ← lists every repo folder
├── app-api/             ← its own git repo (shared with the team)
│   ├── CLAUDE.md        ← the team's contract: Always rules, routing (≤ ~80 lines)
│   ├── docs/claude/     ← workflow details
│   └── docs/codebase/   ← this repo's map
├── app-ui/              ← same structure
└── app-infra/
```

## Who owns what
- **Each repo owns its workflow and map.** Teammates may open a repo directly,
  without your home folder, so a repo's `CLAUDE.md` must stand alone: Always rules,
  routing to `docs/claude/` and `docs/codebase/`. Nothing is moved out of a repo
  into the home folder.
- **The home folder only routes and coordinates.** Repo map, cross-repo rules,
  session state. It never overrides a repo's rules.
- **Conventions stay per repo.** If `app-api` branches from `develop` and `app-ui`
  commits to `main`, both stay; the repo map shows them side by side. Setup may
  point out differences and ask whether to align them.
- **Session state lives only at home.** One `HANDOVER.md` with the branch and state
  of each repo. Per-repo `HANDOVER.md` files are for sessions started inside a repo.

## Cross-repo rules
- One plan and one ticket; one branch and one commit/PR **per repo touched**, each
  following that repo's rules; every PR states the merge order.
- Stage and commit inside each repo; never a blanket add across repos.
- Keep env files out of the repos; canonical copies live in one folder outside them.

## Setting it up (SETUP.md does this with you)
1. Make the folder and clone (or move) the repos into it. Moving local clones is
   fine; git does not care about the parent folder.
2. One plan covering every repo and the home folder, approved once.
3. Repo by repo:
   - **no `CLAUDE.md`**: full adopt (app context, map, short `CLAUDE.md`,
     `docs/claude/`, `docs/codebase/`);
   - **existing `CLAUDE.md`**: the same statement-by-statement review as a single
     repo, restructured only where you approve;
   - each repo gets its own setup branch and commit (or PR), following its rules.
4. Home: `scaffold-home/CLAUDE.md` with the repo map, `HANDOVER.md`, `docs-claude/`.
   `git init` locally, `.gitignore` every repo folder, commit. Never add a remote.
