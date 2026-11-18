# {{PRODUCT_NAME}}: home workspace

This folder is **not** a git repo. It holds independent clones of the product's
repos side by side, so one Claude session sees the whole system. Start Claude here.

## Repo map
| Folder | Role | Stack | Default / dev branch | Own rules |
|---|---|---|---|---|
| `{{repo-api}}/` | {{backend API}} | {{stack}} | `main` / `develop` | `{{repo-api}}/CLAUDE.md` |
| `{{repo-ui}}/` | {{web client}} | {{stack}} | `main` / `develop` | `{{repo-ui}}/CLAUDE.md` |
| `{{repo-infra}}/` | infra only, rarely changed | {{stack}} | `main` | none |

Rule precedence: this file sets the **workflow**; a repo's own `CLAUDE.md` sets the
**technical rules** for that repo and wins on technical questions. Read it before
editing files in that repo.

## Working files (live here, not in any repo)
- `HANDOVER.md`: session state and next steps across all repos.
- `docs-claude/`: specs, per-feature build trackers, decision notes.
  A build tracker starts with "A fresh session reads this file first and continues
  from **Next up**", and is updated at every step boundary.

## Cross-repo work
- One plan, one ticket; one branch and one PR **per repo touched**, same branch name.
- Every PR states the merge order (e.g. "merge api before ui").
- Never stage with a blanket `git add -A` across repos; stage per repo.
- Env files: never commit them; keep canonical copies outside the repos ({{ENV_DIR}}).

## Workflow agreement
{{Paste or link the workflow agreement section from scaffold/CLAUDE.md here;
  it applies to every repo.}}
