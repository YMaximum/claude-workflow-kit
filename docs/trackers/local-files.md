# Tracker: local files

Tickets are Markdown files in the repo, versioned with the code. No setup, no
accounts. Good for solo projects and small teams.

## Layout
```
docs/tickets/
  _TEMPLATE.md
  APP-001-export-monthly-report.md
  APP-002-....md
  done/            # optional: move finished tickets here
```
- ID: a short project prefix plus a zero-padded number (`APP-001`). Take the next
  free number; never reuse one.
- File name: `<ID>-<kebab-slug>.md`.
- Front matter holds `status` (`todo | in-progress | in-review | done | blocked`),
  `labels`, `branch`, `pr`.

## Specs
A small feature's spec lives inside its ticket (Goal, Expected outcome, Acceptance
criteria). A feature that spans several tickets gets `docs/specs/<slug>.md`, and each
ticket links to it.

## Committing to main (no PRs)
Drop the `branch:` and `pr:` fields and the `in-review` status from the template;
record the commit hashes in the progress log instead.

## Rules for Claude
- `/to-tickets` writes one file per ticket from `_TEMPLATE.md`, after the user
  approves the numbered breakdown.
- Starting work: set `status: in-progress` and `branch:`; add a progress-log line.
- Every grill answer goes under **Decisions**, dated.
- Opening a PR: set `status: in-review` and `pr:`.
- Done: `status: done`; move to `done/` if the project uses it.
- Ticket edits are committed together with the work they describe (or in their
  own `docs(tickets): ...` commit when no code changed).

## Finding work
`grep -l "status: todo" docs/tickets/*.md` lists open tickets. A ticket may name
blockers in a `blocked_by:` front-matter list; pick tickets whose blockers are done.
