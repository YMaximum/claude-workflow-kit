# Prerequisites: check, then guide

Claude runs these checks during setup (SETUP step 3) and walks the user through
anything missing (SETUP step 6). Rules:
- **Check first, never assume.** Run the check command; only guide what fails.
- **Explain in plain words**: what the tool is for, in one sentence, then the
  exact command or clicks for *their* OS.
- Steps marked **user** need the user's own hands (installer, admin password,
  browser login). Ask them to run commands with a leading `!` inside Claude Code
  (e.g. `! git --version`) so the output lands in the conversation.
- **Wait and re-check** after each step. If something cannot be fixed now, say what
  is affected, use the fallback, and record it under **Waiting on the user** in
  `HANDOVER.md`.
- Never run `sudo`, installers or system-wide changes yourself without asking.
- Commands that need `sudo` (a password prompt) or open a browser: ask the user to
  run them **in a separate terminal window**, not with `!`, then say "done".
- Give **one step at a time** to a beginner, and say what success looks like
  (e.g. "you should see `v20.x.x`").

## 0. Operating system and shell
Check the exact system, so every command you give fits it:
- `uname -s` → `Linux`, `Darwin` (macOS), or a Windows shell (`MINGW*`/`MSYS*`).
- Linux: `cat /etc/os-release` → `ID` / `ID_LIKE` (ubuntu, debian, linuxmint →
  apt; fedora → dnf; arch → pacman).
- macOS: `sw_vers -productVersion`; check `brew --version` (Homebrew).
On Windows, Claude Code needs Git for Windows (Git Bash); WSL also works.
- Windows without Git Bash or WSL: **user** installs Git for Windows
  (https://git-scm.com/download/win), then restarts Claude Code.
- On Windows, prefer commands that work in Git Bash; avoid `/tmp` (use the session
  scratchpad or the project folder).

## 1. Claude Code
Already running (that is how setup started). Check the version supports plugins:
`/plugin` opens a menu. If not: **user** updates Claude Code (`claude update`, or
re-run the installer from https://claude.com/claude-code), then restarts.
Fallback: skip the plugin; the SETUP skill fallback table covers every role.

## 2. git
Check: `git --version`.
- Missing: **user** installs it. macOS: `xcode-select --install` or
  `brew install git`. Windows: Git for Windows. Debian/Ubuntu: `sudo apt install git`.
  Fedora: `sudo dnf install git`.

Check identity: `git config user.name` and `git config user.email`.
- Empty: ask for name and email, then (with permission)
  `git config --global user.name "<name>"` and
  `git config --global user.email "<email>"`.
  Use their GitHub no-reply email if they prefer privacy.

## 3. Internet access to the kit
Check: the kit was cloned or `SETUP.md` was fetched (it was, if you are reading
this). If a later fetch fails: ask the user to download the repo as a ZIP from
https://github.com/YMaximum/claude-workflow-kit and unzip it next to the project,
then read the files locally.

## 4. Project runtime and toolchain
Match the stack (from the audit, or the user's answer in New mode):
| Stack | Check | Install (user) |
|---|---|---|
| Node.js / npm | `node -v`, `npm -v` | see "Installing Node.js" below; match `.nvmrc` / `engines` if present |
| pnpm / yarn | `pnpm -v` / `yarn -v` | `corepack enable` (ships with Node) |
| Python | `python3 --version` | https://python.org, `brew install python`, or the distro package; plus `uv` or `pip` as the project uses |
| Go | `go version` | https://go.dev/dl |
| Java / Kotlin | `java -version` | a JDK (e.g. Temurin) matching the project's version |
| .NET | `dotnet --version` | https://dotnet.microsoft.com/download |
| Docker (if the project uses it) | `docker --version`, `docker ps` | Docker Desktop, or Docker Engine on Linux |
Other stacks: find the version file (`.tool-versions`, `rust-toolchain`, ...) and
the official installer, and turn its instructions into exact commands for the
user's system before giving them.

### Installing Node.js (exact steps)
- **Linux and macOS (recommended: nvm, no sudo needed).** In a terminal:
  ```
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
  ```
  Close and reopen the terminal (and restart Claude Code), then:
  ```
  nvm install --lts
  node -v        # should print v22.x or newer
  ```
  If the nvm release in that URL is outdated, take the current install line from
  https://github.com/nvm-sh/nvm#installing-and-updating.
- **macOS with Homebrew:** `brew install node`.
- **Windows:** `winget install OpenJS.NodeJS.LTS`, then restart Claude Code.
- Restart Claude Code after installing so it sees the new `PATH`.

Then check the project's commands actually run (Adopt mode): install dependencies
(ask first), run the test command once. Record any that fail as **Gotchas**.
New mode: only the runtime is checked now; the first ticket creates the app.

## 5. Verification tool (only if chosen)
| Chosen method | Check | Install |
|---|---|---|
| Playwright e2e | `npx playwright --version` | `npm i -D @playwright/test` then `npx playwright install` (ask first; downloads browsers) |
| Claude in Chrome / browser tool | the browser tools are available in this session | **user** installs the Claude in Chrome extension and connects it |
| curl / HTTP checks | `curl --version` | usually preinstalled; else the OS package |
| Manual click-checklist | nothing | |

## 6. Tracker tooling
- Local files: nothing.
- GitHub Issues: `docs/trackers/github-issues.md`, "Setup".
- Jira: `docs/trackers/jira.md`, "Setup".

## 7. Models
The kit's agents default to `model: haiku`. If the user's plan does not include
it, set the agents' `model:` to the cheapest allowed model (SETUP placeholder
`{{CHEAP_MODEL}}`).
