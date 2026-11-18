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
Read `GUIDE.md` fully, plus `docs/workflow.md`, `docs/agent-routing.md` and
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
Infer before you ask. Collect what exists:
- `CLAUDE.md`, `AGENTS.md`, `.claude/` (skills, agents, settings)
- `git branch -a`, `git log --oneline -30`: default/dev branch, branch naming,
  commit style
- `git remote -v`; if the remote is GitHub and `gh auth status` succeeds:
  `gh pr list --state merged -L 10` for PR target and title style. Otherwise skip
  and ask in step 4.
- package/build files and CI configs: install, dev, test, lint commands
- ticket keys in branch names and commits (e.g. `ABC-12`): tracker and key prefix
- sibling folders that are git repos: multi-repo?
- `~/.claude/CLAUDE.md`, `~/.claude/model-reference.md`: global rules present?
- installed skills (the skill list you can see): which workflow skills exist?

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
| `{{TRACKER}}`, key prefix, site/project ids, labels | audit (ticket keys) + ask; then the matching `docs/trackers/*.md` |
| `{{SPEC_LOCATION}}` | from the tracker doc (local: in the ticket, `docs/specs/` for multi-ticket features) |
| `{{BRANCHING_RULE}}` | audit; keep the existing convention. No history: recommend `main` for solo, `<KEY>-<n>-<slug>` + PR for teams |
| `{{COMMIT_STYLE}}` | audit (`git log`); confirm |
| `{{PR_RULE}}` | audit (PR target, title style) + who merges (default: the user). `main`-only: "no PRs" |
| `{{TEST_RULE}}` | ask: test-first required? regression test shown failing first? |
| `{{VERIFY_METHOD}}` | ask. Common: "run tests, lint and build, then give the user a click-checklist for the UI"; or an e2e/browser tool |
| Tracker automation (does a branch/PR move tickets?) | ask (Jira / GitHub) |
| Repo shape (single, monorepo, home folder) | audit (sibling repos) + ask |
| `{{ENV_DIR}}` | Home mode only: where canonical env files live outside the repos |
| `{{PRODUCT_NAME}}` and the repo-map rows (`{{repo-api}}`, `{{stack}}`, ...) | Home mode only: one row per sibling repo, from its README, package files and `git branch -a` |
| `{{SUBSCRIPTION_PLAN}}`, `{{EXCLUDED_MODELS}}` | ask: "Which models does your plan include? Any that bill extra?" Default: haiku, sonnet, opus allowed; none excluded |
| Global rules in `~/.claude/` or project-only | ask |
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
commit  "chore: adopt claude workflow" on the agreed branch (asks first)
```
Wait for approval. Adjust and re-present if asked.

## 6. Install
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

**Never overwrite.** Existing files are merged:
- Append new sections; keep existing rules.
- If an existing rule conflicts with a new one (e.g. a different session-start
  routine, or branching), show both and ask which wins. The `Session start` rule
  (read `HANDOVER.md`) must end up present, or handover will not work.
- Existing skills or agents with the same name: keep theirs, report the clash.
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

**Tracker setup**
- Local: create `docs/tickets/`.
- GitHub: `gh auth status` (the user runs `! gh auth login` if needed); create the
  labels listed in the plan.
- Jira: help the user connect the Atlassian MCP (`docs/trackers/jira.md`); confirm
  it works by listing their projects.

**Commit.** If no git repo, `git init`. Commit the setup following the branching
rule just agreed (its own PR if PRs are used). Approval of a plan that lists the
commit covers it; otherwise ask first.

## 7. Verify with a dry run
Ask the user for one small real task (or suggest one). Walk it through the flow up
to an approved plan: describe → grill → spec → ticket(s). Plan mode is optional
here; just write nothing until the spec and the ticket breakdown are approved.
Stop there unless the user wants to go on. Then confirm:
- `CLAUDE.md` has the Session start rule, so a fresh session reads `HANDOVER.md`;
- the ticket exists in the tracker with dated decisions;
- the user knows the phrases: grill (or the mapped skill name), `handover`, `continue`.

## 8. Report
What was installed, what was skipped and why, the skill-name mapping, and the
three phrases the user will use most.
