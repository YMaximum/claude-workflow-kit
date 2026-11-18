# Tracker: GitHub Issues

Tickets are GitHub issues, read and written with the `gh` CLI. Good when the code
already lives on GitHub: issues, branches and PRs link to each other.

## Setup
1. Install the GitHub CLI and run `gh auth login` (the user does this; suggest
   `! gh auth login` inside Claude Code).
2. Check: `gh repo view --json nameWithOwner`.
3. Create labels once:
   ```
   gh label create ready-for-agent --color 5e5eea --description "Spec complete; an agent can pick it up"
   gh label create needs-triage   --color f6b100 --description "Needs a grill session before work"
   gh label create blocked        --color db2828
   ```

## Rules for Claude
- `/to-tickets` creates one issue per ticket (`gh issue create --title ... --body-file ...`)
  using the sections of `scaffold/docs/tickets/_TEMPLATE.md`, after the user approves
  the numbered breakdown. A parent "map" issue lists the children as a task list.
- Read a ticket fully before starting: `gh issue view <n> --comments`.
- Branch: keep the existing convention; if none, `<n>-<kebab-slug>`. The PR body contains `Closes #<n>`.
- Grill answers: post as an issue comment headed **Decisions (YYYY-MM-DD)**.
- Handover / progress: a short issue comment headed **Progress**.
- Never close issues by hand when a PR will close them.

## Finding work
`gh issue list --label ready-for-agent --assignee "" --state open`
