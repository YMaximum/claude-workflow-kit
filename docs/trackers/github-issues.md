# Tracker: GitHub Issues

Tickets are GitHub issues, read and written with the `gh` CLI. Good when the code
already lives on GitHub: issues, branches and PRs link to each other.

## What you need
- A GitHub account with access to the repo, and the repo pushed to GitHub.
- The GitHub CLI (`gh`), installed and logged in.

## Setup (walk the user through each step that is missing)
Claude checks each step first and only guides the user through what is missing.
Steps marked **user** need the user's own hands (a browser login or a password);
inside Claude Code the user can run a command with a leading `!`, e.g.
`! gh auth login`, so its output lands in the conversation.

1. **Is `gh` installed?** Check: `gh --version`.
   If not, the **user** installs it:
   - macOS: `brew install gh`
   - Windows: `winget install --id GitHub.cli`
   - Ubuntu/Debian, Fedora, others: follow https://github.com/cli/cli#installation
     (the official package repository, not the distro's old version)
   Then reopen the terminal (or Claude Code) so `gh` is on the PATH.
2. **Is it logged in?** Check: `gh auth status`.
   If not, the **user** runs `! gh auth login` and picks: GitHub.com → HTTPS →
   "Login with a web browser", then pastes the one-time code in the browser.
   Work accounts on GitHub Enterprise: choose "GitHub Enterprise Server" and enter
   the host name.
3. **Is the repo on GitHub?** Check: `git remote -v` and
   `gh repo view --json nameWithOwner`.
   - No remote yet: offer `gh repo create <name> --private --source . --push`
     (ask first; this creates a repo on the user's account).
   - Remote exists but `gh repo view` fails: the account lacks access; the user
     asks the repo owner for access, or logs in with the right account.
4. **Can it write issues?** Check: `gh issue list -L 1` works. Issues must be
   enabled in the repo settings (Settings → General → Features → Issues).
5. Create labels once (ask first; this changes the repo):
   ```
   gh label create ready-for-agent --color 5e5eea --description "Spec complete; an agent can pick it up"
   gh label create needs-triage   --color f6b100 --description "Needs a grill session before work"
   gh label create blocked        --color db2828
   ```

If the user cannot finish setup now, continue with local files
(`docs/trackers/local-files.md`) and move the tickets into GitHub later with
`gh issue create --body-file <ticket.md>`.

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
