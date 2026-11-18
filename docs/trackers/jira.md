# Tracker: Jira

Tickets live in Jira and Claude reaches them through the Atlassian MCP server.
Good for teams that already plan in Jira.

## What you need
- A Jira Cloud site (`<company>.atlassian.net`) and an account with access to the
  project. (Jira Server/Data Center is not covered by Atlassian's remote MCP.)
- The Atlassian MCP server connected to Claude, authorized with that account.

## Setup (walk the user through each step that is missing)
Claude checks first and guides only through what is missing. Connecting is a
**user** action (a browser login), so Claude explains the steps and waits.

1. **Is it already connected?** Check: are Atlassian/Jira tools available in this
   session (e.g. a tool to search Jira issues)? Try listing the accessible
   Atlassian sites or the Jira projects the user can see. If that works, skip to
   step 3.
2. **Connect it.** Two ways; pick one with the user:
   - **A. claude.ai connector (simplest if they log in to Claude Code with a
     claude.ai account).** In claude.ai: Settings → Connectors → find
     "Atlassian" → Connect → log in to Atlassian and approve. The connector then
     shows up in Claude Code too (restart Claude Code, check with `/mcp`). It is
     account-wide: every project the user opens can use it.
   - **B. Add it to Claude Code directly.** The user runs:
     ```
     ! claude mcp add --transport http atlassian https://mcp.atlassian.com/v2/mcp
     ```
     (add `--scope project` to share the config via `.mcp.json` with the team;
     each person still logs in themselves), then runs `/mcp`, selects
     `atlassian` → Authenticate, and approves in the browser. If the URL has
     changed, use the one from Atlassian's "Remote MCP server" documentation.
   - **Blocked?** Some companies require an Atlassian admin to allow the MCP
     server or the Claude connector first. The user asks their Jira admin.
3. **Check it works:** list the Jira projects the user can see. Note the project
   key (e.g. `APP`) and the site/cloud id.
4. Record in the project's `docs/tracker.md`: site URL and cloud id, project key, board, the issue types you use,
   the label names (`ready-for-agent`, `needs-triage`), and any custom field ids
   (story points, etc.) Claude must fill.

If the user cannot connect now (waiting on an admin, no time), continue: keep the
spec and draft tickets in `HANDOVER.md` or `docs/specs/`, refer to drafts by title,
and create the Jira issues once connected.

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
