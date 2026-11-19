# {{PRODUCT_NAME}}: home workspace

{{PRODUCT_CONTEXT}}
<!-- 2-3 lines: what the product is, for whom, and how the repos fit together. -->

This folder holds the product's repos side by side so one Claude session sees the
whole system. Start Claude here. It is personal: teammates may open a repo directly,
so **each repo's own `CLAUDE.md` is the contract** (workflow + its map). This file
only adds the repo map, cross-repo rules and routing; it never overrides a repo.

## Session start
1. Read `HANDOVER.md` **in this folder**. It is the only session state while you
   work from here (it covers every repo). Ignore per-repo `HANDOVER.md` files.
2. Before working in a repo, read its `CLAUDE.md` and follow its rules (branching,
   commits, tests). Claude Code also loads it when you open files there.
3. Keep `HANDOVER.md` current after every commit and subagent result, with branch
   and state per repo. On "handover", record and stop.

## Repo map
| Folder | Role | Stack | Branching (from its CLAUDE.md) | Read first |
|---|---|---|---|---|
{{REPO_MAP_ROWS}}
<!-- one row per repo, e.g.
| `app-api/` | backend API | Node, Express | `ABC-n-slug` from `develop`, PR | `app-api/CLAUDE.md` |
| `app-infra/` | infra only, rarely changed | Terraform | `main`, PR | `app-infra/CLAUDE.md` (or "none") | -->

## Cross-repo work
- One plan and one ticket for a change that spans repos; one branch and one
  commit/PR **per repo touched**, each following that repo's own rules.
- Every PR (or ticket note) states the merge order, e.g. "merge api before ui".
- Stage and commit inside each repo. Never `git add` across repos from here.
- Env files: never commit them; canonical copies live outside the repos ({{ENV_DIR}}).
- Specs and decisions that span repos: `docs-claude/`. Single-repo ones stay in
  that repo's tracker.

## This folder in git
This folder is a **local-only** git repo (never add a remote, never push). Its
`.gitignore` lists every repo folder, so only `CLAUDE.md`, `HANDOVER.md` and
`docs-claude/` are versioned here. Commit after updating them.
