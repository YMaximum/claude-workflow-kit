# Tracker: Jira

Tickets live in Jira and Claude reaches them through the Atlassian MCP server.
Good for teams that already plan in Jira.

## Setup
1. Connect the Atlassian MCP server: in claude.ai, add the Atlassian connector
   (it is then available in Claude Code), or follow Atlassian's MCP setup docs for a
   local server. Authorize when prompted. This is a user action, and a claude.ai
   connector is account-wide, not per project. Until it is connected, keep specs
   and draft tickets in `HANDOVER.md` or `docs/specs/` and create the issues later.
2. Check: ask Claude to "list the Jira projects I can see". Note the project key
   (e.g. `APP`) and the site/cloud id in the project's `CLAUDE.md`.
3. Record in the project's `docs/tracker.md`: project key, board, the issue types you use,
   the label names (`ready-for-agent`, `needs-triage`), and any custom field ids
   (story points, etc.) Claude must fill.

## Specs and keys
- Specs: the epic (or parent issue) description; long specs may also live in
  `docs/specs/<slug>.md` linked from the epic.
- Jira assigns issue keys on creation. Never invent a key for a ticket that does
  not exist yet; refer to drafts by title.

## Rules for Claude
- `/to-tickets` creates issues (an epic or parent per spec, one child per slice)
  after the user approves the numbered breakdown. Use the ticket sections from
  `scaffold/docs/tickets/_TEMPLATE.md` in the description.
- Read a ticket fully before starting: description, all comments, sub-tasks, linked
  issues, and earlier branches/PRs that mention the key.
- Branch: keep the project's existing convention; if none, `<KEY>-<number>-<kebab-slug>`. PR title: `<KEY>-<number>: <summary>`.
- Grill answers: a comment headed **Decisions (YYYY-MM-DD)**.
- Handover between tickets: a comment headed **Handing on from <KEY>-<n>** on each
  successor ticket: new names and signatures, inherited decisions, what was
  deliberately left undone, anything the ticket got wrong.
- Status: ask whether branch/PR automation moves tickets. If it does, never transition by hand unless it
  failed, and say so when you do.

## Finding work
JQL: `project = APP AND labels = ready-for-agent AND status = "To Do" AND assignee is EMPTY`
