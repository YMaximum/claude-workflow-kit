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
   If not, the **user** installs it (in a separate terminal; it asks for a password
   on Linux):
   - macOS: `brew install gh`
   - Windows: `winget install --id GitHub.cli`
   - Ubuntu, Debian, Linux Mint, Pop!_OS (official repository):
     ```
     (type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
     && sudo mkdir -p -m 755 /etc/apt/keyrings \
     && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
     && cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
     && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
     && sudo mkdir -p -m 755 /etc/apt/sources.list.d \
     && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
     && sudo apt update \
     && sudo apt install gh -y
     ```
   - Fedora: `sudo dnf install gh`
   - Arch: `sudo pacman -S github-cli`
   - Anything else, or if a command above fails: https://github.com/cli/cli#installation
   Then restart Claude Code so `gh` is on the PATH, and re-check `gh --version`.
2. **Is it logged in?** Check: `gh auth status`.
   If not, the **user** runs `! gh auth login` and picks: GitHub.com → HTTPS →
   "Login with a web browser", then pastes the one-time code in the browser.
   Work accounts on GitHub Enterprise: choose "GitHub Enterprise Server" and enter
   the host name.
   **User says it worked but `gh auth status` still fails?** Go through these
   before falling back:
   - Did they log in **inside Claude Code** with `! gh auth login`, or in a terminal
     as the same OS user? A login under another user or machine does not count.
   - Was `gh` installed *after* Claude Code started? Restart Claude Code, re-check.
   - Run `gh auth status -h github.com` and read the exact error to them.
   - Token problems: `! gh auth refresh -h github.com`, or log out and in again
     (`! gh auth logout`, then `! gh auth login`).
   Still failing: use "GitHub not ready yet" below and keep going.
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

## GitHub not ready yet (fallback recipe)
If `gh`, login or the repo cannot be finished now, continue with local drafts:
1. Copy `scaffold/docs/tickets/_TEMPLATE.md` to `docs/tickets/_TEMPLATE.md`.
2. Name drafts `docs/tickets/draft-001-<slug>.md`, `draft-002-...` (GitHub assigns
   the real `#n` later; never invent one). Front matter: `github: pending`.
3. `docs/tracker.md` says: "Target: GitHub Issues (`<owner>/<repo>`). Status:
   pending (<reason>). Until then, drafts live in `docs/tickets/`."
4. `HANDOVER.md` → **Waiting on the user**: the exact next step (e.g. "run
   `! gh auth login`, then say 'connect GitHub'").
5. When connected: for each draft, `gh issue create --title "<title>" --body-file
   <draft>`; replace references to the draft (in specs, other drafts,
   `HANDOVER.md`) with the returned `#<n>`; then delete the draft, since the issue
   is now the source of truth. Set `docs/tracker.md` status to active and remove
   the waiting item.

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
