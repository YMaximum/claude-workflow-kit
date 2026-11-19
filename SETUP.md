# SETUP: instructions for Claude

**You are Claude, and the user asked you to set up this workflow.** Follow these
steps in order. Talk with the user in the language they write in. Write no file
until the plan in step 5 is approved.

Get the kit first, into a scratch directory (not the user's project):
```
git clone --depth 1 https://github.com/YMaximum/claude-workflow-kit <scratch>/claude-workflow-kit
```
`<scratch>` = your session scratchpad if you have one, else e.g. `/tmp`. Note the
commit (`git -C <scratch>/claude-workflow-kit rev-parse --short HEAD`) for `{{KIT_SHA}}`.
If cloning is impossible, fetch files from the raw base
`https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/<path>`.

**Precedence, everywhere below:** the project's existing conventions and the user's
answers win over the kit's examples. The kit's names (`APP-000`, `develop`,
`<KEY>-<n>-<slug>`) are examples, never requirements.

---

## 1. Learn the workflow
Read `GUIDE.md` fully, plus `docs/prerequisites.md`, `docs/workflow.md`, `docs/agent-routing.md` and
`docs/handover.md`. Read the other docs only when they apply: the chosen tracker's
`docs/trackers/*.md`, `docs/adopting-existing-project.md` in Adopt mode,
`docs/home-pattern.md` in Home mode, `docs/skills.md` when mapping skills.

## 2. Pick the mode
- **New**: the folder has no project code yet (empty, or only a README).
  Do **not** scaffold the app during setup. Record the intended commands (from the
  user's stack) and make "create the app skeleton" part of the first ticket.
- **Adopt**: the folder has existing code, with or without a `CLAUDE.md`.
  Also read `docs/adopting-existing-project.md`.
- **Home**: the user wants several repos under one parent folder
  (`docs/home-pattern.md`). Combine with New or Adopt for each repo.

If the folder is not a git repo, plan a `git init` (step 5 lists it).

## 3. Audit (read-only)
First run the checks in `docs/prerequisites.md` (OS and shell, Claude Code plugin
support, git and git identity, the project's runtime, the verification tool once
chosen, tracker tooling). Note what is missing; do not install anything yet.

Then infer the project's conventions before you ask. Collect what exists:
- `CLAUDE.md`, `AGENTS.md`, `.claude/` (skills, agents, settings)
- `git branch -a`, `git log --oneline -30`: default/dev branch, branch naming,
  commit style
- `git remote -v`; if the remote is GitHub and `gh auth status` succeeds:
  `gh pr list --state merged -L 10` for PR target and title style. Otherwise skip
  and ask in step 4.
- package/build files and CI configs: install, dev, test, lint commands. Check that
  each inferred command can actually run (dependencies installed, config present);
  flag the ones that cannot instead of recording them as working.
- ticket keys in branch names and commits (e.g. `ABC-12`): tracker and key prefix
- sibling folders that are git repos: multi-repo?
- `~/.claude/CLAUDE.md`, `~/.claude/model-reference.md`: global rules present?
- tracker tooling: `gh --version` and `gh auth status`; whether Atlassian/Jira MCP
  tools are available in this session. Report what is missing; it gets set up in
  step 6, not assumed.
- installed skills: the skills available in *this* session, which are the user's
  own setup. Note which workflow skills exist (often `grilling`, `tdd`; names change).
- working tree: `git status --short`. Uncommitted work belongs to the user: never
  stash, reset, switch branches or commit it. Note it for step 6.
- an existing status/notes file that sessions are told to read (e.g. `docs/STATUS.md`).

For a large project, delegate the reading to a cheap read-only subagent and ask
for a short report. Show the user a short summary of what you inferred.

## 4. Grill the user
Ask with a recommended answer each time. Confirm inferences; ask only about gaps
and conflicts. Stop when every row that applies has a value.

To avoid over-asking: put rows with an obvious default (commit style, key prefix,
date, language, models) into **one** message: "Here are my defaults; change any?"
Ask the real decisions (what to adopt, tracker, branching, tests, verification,
global rules) one at a time. If the user answers ahead, take the answer and skip
that question.

| Placeholder / decision | Where the value comes from |
|---|---|
| What to adopt (all, or a subset) | ask |
| `{{PROJECT_NAME}}`, `{{ONE_LINE_DESCRIPTION}}` | README / package file; confirm |
| `{{INSTALL_COMMAND}}` `{{DEV_COMMAND}}` `{{TEST_COMMAND}}` `{{LINT_COMMAND}}` | package/build files; ask if none |
| `{{TRACKER}}`, key prefix, site/project ids | audit (ticket keys) + ask; then the matching `docs/trackers/*.md` |
| Labels (`ready-for-agent`, `needs-triage`, `blocked`) | ask whether to use them, or map to the team's existing labels |
| `{{SPEC_LOCATION}}` | from the tracker doc (local: in the ticket, `docs/specs/` for multi-ticket features) |
| `{{BRANCHING_RULE}}` | audit; keep the existing convention (a `develop` branch means branch from and PR to it). Only with no convention at all: recommend `main` for solo, `<KEY>-<n>-<slug>` + PR for teams |
| `{{COMMIT_STYLE}}` | audit (`git log`); confirm |
| `{{PR_RULE}}` | audit (PR target, title style) + who merges (default: the user). `main`-only: "no PRs" |
| `{{TEST_RULE}}` | ask: test-first required? regression test shown failing first? |
| `{{VERIFY_METHOD}}` | ask. Common: "run tests, lint and build, then give the user a click-checklist for the UI"; or an e2e/browser tool |
| Tracker automation (does a branch/PR move tickets?) | ask (Jira / GitHub) |
| Repo shape (single, monorepo, home folder) | audit (sibling repos) + ask |
| `{{ENV_DIR}}` | Home mode only: where canonical env files live outside the repos |
| `{{PRODUCT_NAME}}` and the repo-map rows (`{{repo-api}}`, `{{stack}}`, ...) | Home mode only: one row per sibling repo, from its README, package files and `git branch -a` |
| `{{CHEAP_MODEL}}` | the cheapest allowed model for the `scout`/`implementer` agents (default `haiku`) |
| `{{SUBSCRIPTION_PLAN}}`, `{{EXCLUDED_MODELS}}` | ask: "Which models does your plan include? Any that bill extra?" Default: haiku, sonnet, opus allowed; none excluded |
| Global rules in `~/.claude/` or project-only | ask. Project-only: put the routing rules in the project `CLAUDE.md` and skip `model-reference.md` (or keep it at `docs/model-reference.md`) |
| `HANDOVER.md` committed or per-developer | solo: committed. Team: recommend per-developer and gitignored (see `docs/handover.md`), with any shared status file kept as is |
| Existing status file vs `HANDOVER.md` | ask, offering: (a) keep it as the shared team status and add a local `HANDOVER.md`; (b) replace it with `HANDOVER.md`; (c) keep only it and point the handover skill at it |
| Ticket key for the setup commit | if branches need a ticket key: ask for one (or a "chore" branch name the team accepts) |
| Docs language | the user's language unless they say otherwise |
| `{{DATE}}` | today |
| `{{ADOPTED_PARTS}}` | the adoption answer (e.g. "all" or "handover, agent routing") |
| `{{KIT_SHA}}` | the kit commit noted when cloning |
| `{{GRILL_SKILL}}` `{{SPEC_SKILL}}` `{{TICKETS_SKILL}}` `{{IMPLEMENT_SKILL}}` | installed skill names (step 6), or "none" |

## 5. Present the plan
List every action, one line each. Example (GitHub Issues, adopt mode):
```
append  CLAUDE.md                     sections: Session start, Workflow agreement, Agent routing (diff shown)
create  HANDOVER.md                   bootstrapped from open branches / uncommitted work / open PRs
create  docs/tracker.md               from docs/trackers/github-issues.md, examples rewritten with real values
create  .claude/agents/scout.md, implementer.md
create  .claude/skills/handover/SKILL.md
run     gh label create ready-for-agent / needs-triage / blocked
append  ~/.claude/CLAUDE.md           orchestrator rules (only if chosen)
create  ~/.claude/model-reference.md  with the user's models (only if chosen)
user    install workflow skills       see step 6
user    connect the tracker           only what is missing, e.g. "install gh, then ! gh auth login"
user    install prerequisites         only what is missing, e.g. "install Node 20 LTS", "set git identity"
commit  "chore: adopt claude workflow" on the agreed branch (asks first)
```
Wait for approval. Adjust and re-present if asked.

## 6. Install
**Prerequisites first.** For each item `docs/prerequisites.md` found missing:
explain in one sentence what it is for, give the exact command or clicks for the
user's OS, wait until they say it is done, and re-check. Ask before running any
install or `git config --global` yourself. If something cannot be fixed now, use
the fallback in that doc and record it in `HANDOVER.md` under **Waiting on the
user**. Continue with the rest of setup; do not stall.

**Files**
- Copy from `scaffold/` (plus `scaffold-home/`, `scaffold-global/` if chosen).
  Replace every `{{PLACEHOLDER}}` and delete the guidance HTML comments.
- `docs/tracker.md` = the chosen tracker doc, with every example (`APP`, `ABC`, JQL,
  label names, branch shape) rewritten to the project's real values. Record the
  Jira site/cloud id there.
- Ticket template: local tracker → `docs/tickets/_TEMPLATE.md` with the real key
  prefix; if committing to `main` without PRs, drop the `branch:`/`pr:` fields and
  the `in-review` status. Jira / GitHub → do not copy it; use its sections as the
  issue description.
- After writing, grep the project (and `~/.claude/` files you wrote) for `{{` and
  `<!--`; nothing may remain.

**Uncommitted work in the tree.** Do the setup in a separate git worktree on a new
branch (`git worktree add ../<repo>-setup -b <setup-branch> <base-branch>`), or ask the
user to commit first. Never stash, switch or reset the user's tree.

**Never overwrite.** Existing files are merged:
- Append new sections; keep existing rules.
- If an existing rule conflicts with a new one (e.g. a different session-start
  routine, or branching), show both and ask which wins. The `Session start` rule
  (read `HANDOVER.md`) must end up present, or handover will not work.
- Existing skills or agents with the same name: keep theirs, report the clash, and
  rewrite the routing text in `CLAUDE.md` to describe *their* agent (its real model
  and purpose), e.g. "use `scout` (sonnet) for searches, or pass `model: haiku` for
  plain scans".
- `~/.claude/` files: same rules.

**Home folder** (if chosen): first create the parent with `scaffold-home/CLAUDE.md`
and the repo map, and put the workflow agreement there (it applies to all repos).
Each repo's own `CLAUDE.md` keeps only its technical rules.

**Workflow skills.** The user installs Matt Pocock's collection
(https://github.com/mattpocock/skills). In Claude Code this is usually
`/plugin marketplace add mattpocock/skills`, then installing it from `/plugin`;
follow that repo's README if this differs. Afterwards, map the installed skill
names to the four roles and fill the **Skills** table in `CLAUDE.md`:

| Role | Look for | If missing |
|---|---|---|
| grill | `grill-me`, `grilling` | ask one question at a time with a recommended answer, until nothing is unclear |
| spec | `to-spec`, `to-prd` | write the spec as described in `docs/workflow.md` §3 |
| tickets | `to-tickets`, `to-issues` | split as described in `docs/workflow.md` §4 |
| implement | `implement`, `tdd` | follow `docs/workflow.md` §5 |

The workflow works without the plugin; the skills just make it consistent.

**Tracker setup.** Check before assuming. For every missing piece, explain the
step in plain words, give the exact command or clicks from the tracker doc, and
wait for the user to say it is done; then re-check. Commands that need the user's
own login, they run with a leading `!` (e.g. `! gh auth login`).
- Local: create `docs/tickets/`. Nothing else is needed.
- GitHub (`docs/trackers/github-issues.md`, "Setup"): `gh --version` → install if
  missing; `gh auth status` → log in if needed; `gh repo view` → create or get
  access to the repo; `gh issue list -L 1` → issues enabled; then the labels.
- Jira (`docs/trackers/jira.md`, "Setup"): check whether Atlassian/Jira tools are
  available in this session; if not, guide the user through connecting (claude.ai
  connector or `claude mcp add`), including the admin-approval case. A claude.ai
  connector is account-wide, not per project. Confirm by listing their projects. If it is not
  connected yet, continue: keep specs and draft tickets in `HANDOVER.md` (or
  `docs/specs/`) and create the Jira issues once it is connected. Jira assigns issue
  keys; never invent one.

**Commit.** If no git repo, `git init`. Commit the setup following the branching
rule just agreed (its own PR if PRs are used). Approval of a plan that lists the
commit covers it; otherwise ask first.

## 7. Verify with a dry run
Run it where the new `CLAUDE.md` exists: the setup worktree/branch if the setup is
not merged yet (tell the user it takes effect for everyone after merge).
Ask the user for one small real task (or suggest one). Walk it through the flow up
to an approved plan: describe → grill → spec → ticket(s). Plan mode is optional
here; just write nothing until the spec and the ticket breakdown are approved.
Stop there unless the user wants to go on. Then confirm:
- `CLAUDE.md` has the Session start rule, so a fresh session reads `HANDOVER.md`;
- the ticket exists in the tracker with dated decisions (or, if the tracker is not
  connected yet, the draft is recorded with a note to create it later);
- the user knows the phrases: grill (or the mapped skill name), `handover`, `continue`.

## 8. Onboard the user
The user should leave knowing how to work this way, not only that files exist.
In a short message (in their language):
1. The flow in one picture: describe → grill → spec → tickets → implement →
   review → (fixes → grill) → merge. Grill any time a decision is needed.
2. The phrases they will use: "grill me", "implement <ticket>", "handover",
   "continue" (and `/clear`). Point to the cheat sheet now in their `CLAUDE.md`.
3. What is still pending (prerequisites, tracker connection, plugin) and the exact
   next step for each.
4. Offer to start their first real feature now.

## 9. Report
What was installed, what was skipped and why, the skill-name mapping, and anything
waiting on the user.
