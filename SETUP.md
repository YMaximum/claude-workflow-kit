# SETUP: instructions for Claude

**You are Claude, and the user asked you to set up this workflow.** Follow these
steps in order. Talk with the user in the language they write in. Write no file
until the plan in step 5 is approved.

**Assume the user may be a beginner** until they show otherwise: plain words, one
step at a time, and explain every kit term in one line the first time you use it
(`docs/glossary.md`). Experienced users can say "skip the explanations".

Get the kit first, into a scratch directory (not the user's project):
```
git clone --depth 1 https://github.com/YMaximum/claude-workflow-kit <scratch>/claude-workflow-kit
```
`<scratch>` = your session scratchpad if you have one, else e.g. `/tmp`. Note the
commit (`git -C <scratch>/claude-workflow-kit rev-parse --short HEAD`) for `{{KIT_SHA}}`.
If cloning is impossible, fetch files from the raw base
`https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/<path>`.

**Setup never changes application code.** It writes workflow and docs files only.
Bugs or gaps found on the way (a broken test script, missing dependencies, no lint
config) become tickets, reported in the summary; fix them only if the user asks.

**Precedence, everywhere below:** the project's existing conventions and the user's
answers win over the kit's examples. The kit's names (`APP-000`, `develop`,
`<KEY>-<n>-<slug>`) are examples, never requirements.

---

## 1. Learn the workflow
Read `GUIDE.md` fully, plus `docs/glossary.md`, `docs/prerequisites.md`, `docs/workflow.md`, `docs/agent-routing.md` and
`docs/handover.md`. Read the other docs only when they apply: the chosen tracker's
`docs/trackers/*.md`, `docs/adopting-existing-project.md` in Adopt mode,
`docs/home-pattern.md` in Home mode, `docs/skills.md` when mapping skills,
`docs/codebase-map.md` in Adopt mode (and in New mode for the app-context part).

## 2. Pick the mode
- **New**: the folder has no project code yet (empty, or only a README).
  Do **not** scaffold the app during setup. Record the intended commands (from the
  user's stack) and make "create the app skeleton" part of the first ticket.
- **Adopt**: the folder has existing code, with or without a `CLAUDE.md`.
  Also read `docs/adopting-existing-project.md`.
- **Home**: the user wants several repos under one parent folder
  (`docs/home-pattern.md`). Each repo is handled as New or Adopt on its own (some
  may already have a `CLAUDE.md`, some not). Each repo's `CLAUDE.md` stays the
  team's contract; the home folder only adds the repo map, cross-repo rules,
  routing, and the one `HANDOVER.md`.

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

Message order: (1) the audit summary, with the five words the user will hear most
folded in, one line each from `docs/glossary.md`: **grill, spec, ticket, commit,
handover**; (2) the step 3b app-context batch; (3) the defaults; (4) the remaining
decisions. Keep each message short; a beginner should never get all four at once.

## 3b. Map the codebase and agree the app context
Follow `docs/codebase-map.md`.
- **Adopt mode:** survey the code (delegate per area to cheap scouts if large),
  draft the four app-context topics (purpose and users, domain terms, critical
  flows and no-go zones, direction and constraints), and discuss them with the
  user. Map the overview, glossary and the 2-3 most active areas now; other areas
  are mapped when a ticket first touches them.
- **Existing `CLAUDE.md`:** review it statement by statement against the code
  and propose a restructure: a table with **one action per row** (keep / fix /
  move to `<file>` / remove?), then the proposed new root **in full** and a list of
  moved blocks with their destination. Apply only what the user approves (step 6).
- **New mode:** no code to map. Discuss the app context only; `docs/codebase/`
  starts with the overview and glossary, and area files are created by tickets.
- **Home mode:** do this step **per repo**, inside each repo:
  - repo without `CLAUDE.md`: full adopt (app context for that repo, its map);
  - repo with `CLAUDE.md`: the same statement-by-statement review as above;
  - workflow rules stay in each repo (teammates may open it without your home
    folder); never move them out to the home folder.
  Then draft the product-level context (how the repos fit together) for the home
  `CLAUDE.md`. Audit each repo's conventions; keep them per repo, and only point
  out differences ("api branches from develop, ui commits to main: align?").

## 4. Grill the user
Ask with a recommended answer each time. Confirm inferences; ask only about gaps
and conflicts. Stop when every row that applies has a value.

**Batching rule (applies to every grill, here and later):** questions that are
independent of each other go together, up to ~4 per message. A question whose
options depend on an earlier answer is never in the same batch; ask it after that
answer. Typical dependent pairs, never batched together: tracker → its labels and
key; branching → PR rules; data model (e.g. delete vs status column) → storage,
schema or migration questions; "is X legacy?" → "which folders exactly?".

To avoid over-asking: put rows with an obvious default (commit style, key prefix,
date, language, models) into **one** message: "Here are my defaults; change any?"
Explain each default in plain words; never ask a question the user cannot be
expected to answer (e.g. model availability): state the default instead.
Then batch the real decisions that are independent (e.g. tests, verification,
global rules); ask dependent ones after their answer (e.g. tracker first, then its
labels and key; branching first, then PR rules). If the user answers ahead, take
the answer and skip that question.

| Placeholder / decision | Where the value comes from |
|---|---|
| What to adopt (all, or a subset) | ask. Home mode: per repo, **full adopt / review only / none** (e.g. a rarely touched infra repo can be skipped), plus whether each repo's setup is committed now or **deferred** |
| `{{ADOPTED_FOOTER}}` | solo or the user wants it: `## Adopted from claude-workflow-kit` + the adopted parts, `claude-workflow-kit@{{KIT_SHA}}`, the date. Team repos where it would confuse teammates: empty (record the kit version in the setup commit message instead) |
| `{{PROJECT_NAME}}` | README / package file; confirm |
| `{{APP_CONTEXT}}`, `{{KEY_TERMS}}`, `{{GOTCHAS}}` | step 3b discussion: 3-5 lines of context, the 3-6 key terms, the top 3-5 gotchas |
| `{{APP_PURPOSE_DETAIL}}` `{{LAYOUT}}` `{{DATA_FLOW}}` `{{AREA_TABLE}}` `{{CRITICAL_FLOWS}}` `{{EXTERNAL_SYSTEMS}}` `{{DIRECTION}}` | step 3b survey + discussion (`docs/codebase/README.md`); New mode: what is known, "none yet" otherwise |
| `{{DOMAIN_TERMS}}` | step 3b discussion (`docs/codebase/glossary.md`), one table row per term |
| `{{AREA_ROUTES}}` | one routing-table row per area mapped now; empty in New mode |
| `{{INSTALL_COMMAND}}` `{{DEV_COMMAND}}` `{{TEST_COMMAND}}` `{{LINT_COMMAND}}` | package/build files; ask if none |
| `{{TRACKER}}`, key prefix, site/project ids | audit (ticket keys) + ask; then the matching `docs/trackers/*.md` |
| Labels (`ready-for-agent`, `needs-triage`, `blocked`) | ask whether to use them, or map to the team's existing labels |
| `{{SPEC_LOCATION}}` | from the tracker doc (local: in the ticket, `docs/specs/` for multi-ticket features) |
| `{{BRANCHING_RULE}}` | audit; keep the existing convention (a `develop` branch means branch from and PR to it). Only with no convention at all: recommend `main` for solo, `<KEY>-<n>-<slug>` + PR for teams |
| `{{COMMIT_STYLE}}` | audit (`git log`); confirm |
| `{{PR_RULE}}` | audit (PR target, title style) + who merges (default: the user). `main`-only: "no PRs". Branches but no remote: "review the branch diff locally; the PR description goes in the ticket; the user merges locally" |
| `{{TEST_RULE}}` | ask: test-first required? regression test shown failing first? |
| `{{VERIFY_METHOD}}` | ask. Common: "run tests, lint and build, then give the user a click-checklist for the UI"; or an e2e/browser tool |
| Tracker automation (does a branch/PR move tickets?) | ask (Jira / GitHub) |
| Repo shape (single, monorepo, home folder) | audit (sibling repos) + ask |
| `{{ENV_DIR}}` | Home mode only: where canonical env files live outside the repos |
| `{{PRODUCT_NAME}}`, `{{PRODUCT_CONTEXT}}` | Home mode only: product name and 2-3 lines on how the repos fit together (step 3b) |
| `{{REPO_MAP_ROWS}}` | Home mode only: one row per repo: role, stack, branching rule taken from that repo's `CLAUDE.md` |
| `{{CHEAP_MODEL}}` | default `haiku` (the cheapest Claude model). Do not ask a beginner; if a subagent call later fails because the model is unavailable, switch to the cheapest model shown by `/model` |
| `{{SUBSCRIPTION_PLAN}}`, `{{EXCLUDED_MODELS}}` | do not ask a beginner. Default: "the user's Claude plan", excluded "none known". Tell them: "type `/model` any time to see which models you have." Ask only users who mention plan limits or extra-billed models |
| Global rules in `~/.claude/` or project-only | ask. Project-only: put the routing rules in the project `CLAUDE.md` and skip `model-reference.md` (or keep it at `docs/model-reference.md`) |
| `HANDOVER.md` committed or per-developer | solo: committed. Team: recommend per-developer and gitignored (see `docs/handover.md`), with any shared status file kept as is |
| Existing status file vs `HANDOVER.md` | ask, offering: (a) keep it as the shared team status and add a local `HANDOVER.md`; (b) replace it with `HANDOVER.md`; (c) keep only it and point the handover skill at it |
| Ticket key for the setup commit | if branches need a ticket key: ask for one (or a "chore" branch name the team accepts) |
| Docs language | the user's language unless they say otherwise |
| `{{DATE}}` | today |
| `{{KIT_SHA}}` | the kit commit noted when cloning |
| `{{GRILL_SKILL}}` `{{SPEC_SKILL}}` `{{TICKETS_SKILL}}` `{{IMPLEMENT_SKILL}}` | installed skill names (step 6), or "none" |

## 5. Present the plan
List every action, one line each. Example (GitHub Issues, adopt mode):
```
user    install prerequisites         only what is missing, e.g. "install Node LTS", "set git identity"
user    connect the tracker           only what is missing, e.g. "install gh, then ! gh auth login"
restruct CLAUDE.md                    root ≤ ~80 lines; approved keep/fix/move items applied (diff shown)
create  docs/claude/workflow.md       required (agents.md, coaching.md optional: may be merged in)
create  docs/codebase/README.md, glossary.md, api.md, data.md (2-3 active areas), _AREA_TEMPLATE.md
create  docs/codebase/domain-rules.md, docs/claude/coding-style.md   only if there is content for them
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
Home mode: **one plan**, approved once, with a section per repo (its own lines as
above, and its own setup branch/commit following that repo's rules) and a section
for the home folder (`CLAUDE.md`, `HANDOVER.md`, `docs-claude/`, `.gitignore`,
local `git init`).

Wait for approval. Adjust and re-present if asked.

## 6. Install
**Prerequisites first.** For each item `docs/prerequisites.md` found missing:
explain in one sentence what it is for, give the exact command or clicks for the
user's exact system (`docs/prerequisites.md` §0: distro, Homebrew, Windows), say
what success looks like, wait until they say it is done, and re-check. Ask before running any
install or `git config --global` yourself. If something cannot be fixed now, use
the fallback in that doc and record it in `HANDOVER.md` under **Waiting on the
user**. Continue with the rest of setup; do not stall.

**Files**
- Copy from `scaffold/` (plus `scaffold-home/`, `scaffold-global/` if chosen),
  including `docs/claude/` and `docs/codebase/`. Replace every `{{PLACEHOLDER}}`
  and delete the guidance HTML comments. Area files come from
  `docs/codebase/_AREA_TEMPLATE.md` (keep the template for later areas).
- Root `CLAUDE.md` must end up ≤ ~80 lines. Anything longer moves to a routed file.
- Required: `CLAUDE.md`, `HANDOVER.md`, `docs/claude/workflow.md`,
  `docs/codebase/README.md`. Optional (the user may drop or merge them):
  `docs/claude/agents.md` and `coaching.md` (merge into `workflow.md`),
  `docs/codebase/glossary.md` (merge into the README), `_AREA_TEMPLATE.md` (then
  new area files follow the sections listed in `docs/codebase-map.md`). When one
  is dropped, update every reference to it (routing table, `workflow.md`,
  onboarding) so nothing points at a missing file.
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

**No-touch folders** (frozen legacy, generated code, vendored code): record the
rule as a root **Gotchas** line. Do not add a nested `CLAUDE.md` inside them unless
the user agrees, because adding a file is itself a change to that folder. Nested
`CLAUDE.md` files suit folders where adding a file is fine.

**Never overwrite.** Existing files are merged:
- The `Session start` rule (read `HANDOVER.md` if present) is required in a
  single repo. In home mode it is optional in each repo, since the home
  `HANDOVER.md` is the session state there; keep it if teammates use per-repo
  handovers.
- Existing `CLAUDE.md`: apply the restructure approved in step 3b. Content moved out
  goes to the routed file named in the review table; nothing is deleted without
  approval. Other existing files: append new sections; keep existing rules.
- If an existing rule conflicts with a new one (e.g. a different session-start
  routine, or branching), show both and ask which wins. Outside home mode the
  `Session start` rule must end up present, or handover will not work.
- Existing skills or agents with the same name: keep theirs, report the clash, and
  rewrite the routing text in `CLAUDE.md` to describe *their* agent (its real model
  and purpose), e.g. "use `scout` (sonnet) for searches, or pass `model: haiku` for
  plain scans".
- `~/.claude/` files: same rules.

**Home folder** (if chosen), in this order:
1. Repo by repo, as chosen (full adopt / review only / none): install as in a
   single repo (files, restructure, map), each with its own setup branch and
   commit (or PR) per that repo's rules. Workflow rules stay in each repo; do not
   move them to the home folder.
   **Deferred repo** (no ticket key yet, or someone else commits there): prepare
   the changes on a scratch copy, save them as
   `docs-claude/drafts/<repo>-setup.patch` (check it applies cleanly to the repo's
   base branch with `git apply --check`), leave the repo untouched, and add a
   **Waiting on the user** item: who applies it, when, and the exact command.
   Uncommitted-by-request is also fine: leave files in the working tree and say so.
2. Home: copy `scaffold-home/` (`CLAUDE.md` with the repo map, branching per repo
   read from each repo's `CLAUDE.md`; `.claude/` agents and handover skill;
   `docs-claude/coaching.md`), plus `HANDOVER.md` (the only session state, a
   section per repo) and `docs-claude/drafts/`.
3. Home in git, **local only**: `git init`, a `.gitignore` listing every repo
   folder, commit. Never add a remote or push.
4. Per-repo `HANDOVER.md` files are not created by a home setup (they are for
   sessions started inside a repo, and gitignored there).

**Workflow skills.** The user installs Matt Pocock's collection
(https://github.com/mattpocock/skills). In Claude Code this is usually
`/plugin marketplace add mattpocock/skills`, then installing it from `/plugin`;
follow that repo's README if this differs. Then the user **restarts Claude Code**
and types `/` to see the skill list; confirm the skills appear before relying on
them. Map the installed skill names (they may carry a plugin prefix, e.g.
`mattpocock-skills:grilling`) to the four roles and fill the **Skills** table in
`docs/claude/workflow.md`. Plugins are installed **per person**: in a team, each
teammate installs them, and the "If missing" column is what everyone can rely on.

| Role | Look for | If missing |
|---|---|---|
| grill | `grill-me`, `grilling` | questions with recommended answers, batched by the rule in step 4, until nothing is unclear |
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
  `docs/specs/`; in home mode, the home `docs-claude/drafts/`) and create the Jira
  issues once it is connected. Jira assigns issue
  keys; never invent one.
- Tracker not ready (GitHub or Jira): follow the tracker doc's "not ready yet"
  recipe (local drafts, `pending` status, exact next step in `HANDOVER.md`).

**Commit.** If no git repo, `git init`. Commit the setup following the branching
rule just agreed (its own PR if PRs are used). Approval of a plan that lists the
commit covers it; otherwise ask first. The setup commit holds only setup files;
summarise a `CLAUDE.md` restructure in its message for teammates.

## 7. Verify with a dry run
Run it where the new `CLAUDE.md` exists: the setup worktree/branch if the setup is
not merged yet (tell the user it takes effect for everyone after merge).
Ask the user for one small real task (or suggest one). Walk it through the flow up
to an approved plan: describe → grill → spec → ticket(s). Plan mode is optional
here; just write nothing until the spec and the ticket breakdown are approved.
Stop there unless the user wants to go on. Commit the dry-run spec and tickets
**separately** from the setup commit (same branch, own commit; or after the setup
is merged), so the setup can be reviewed on its own. Then confirm:
- `CLAUDE.md` has the Session start rule, so a fresh session reads `HANDOVER.md`;
- root `CLAUDE.md` is ≤ ~80 lines and every routed file it names exists;
- the dry-run ticket names which `docs/codebase/` area it touches (mapped or to map);
- the ticket exists in the tracker with dated decisions (or, if the tracker is not
  connected yet, the draft is recorded with a note to create it later);
- the user knows the phrases: grill (or the mapped skill name), `handover`, `continue`.

## 8. Onboard the user
The user should leave knowing how to work this way, not only that files exist.
In a short message (in their language):
1. The flow in one picture: describe → grill → spec → tickets → implement →
   review → (fixes → grill) → merge. Grill any time a decision is needed.
2. The phrases they will use: "grill me", "implement <ticket>", "handover",
   "continue", and `/clear`, with why: long chats get slower and cost more, and
   `HANDOVER.md` carries what matters into the fresh session. Point to the cheat
   sheet in `docs/claude/coaching.md` (home mode: `docs-claude/coaching.md`; or
   wherever it was merged), routed from `CLAUDE.md`.
3. What is still pending (prerequisites, tracker connection, plugin) and the exact
   next step for each.
4. Offer to start their first real feature now.

## 9. Report
What was installed, what was skipped and why, the skill-name mapping, and anything
waiting on the user.
