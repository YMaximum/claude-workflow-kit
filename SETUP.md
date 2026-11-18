# SETUP: instructions for Claude

**You are Claude, and the user asked you to set up this workflow.** Follow these
steps in order. Talk with the user in the language they write in. Do not write any
file until step 5 is approved.

Repo raw base (for fetching files when you are not inside a clone):
`https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/`
Easiest: `git clone https://github.com/YMaximum/claude-workflow-kit /tmp/claude-workflow-kit`
(or any scratch directory) and read from there.

---

## 1. Learn the workflow
Read `GUIDE.md` fully, then skim `docs/`. You will apply it, so understand
the "why" of each part: workflow agreement, skills, the flow with three grill
points, tickets, agent routing, home pattern, handover.

## 2. Pick the mode
Ask (or infer from the request):
- **New**: an empty or brand-new project.
- **Adopt**: an existing project that does not use this workflow yet.
  Read `docs/adopting-existing-project.md`.

## 3. Audit (Adopt mode, and New mode if a folder already exists)
Read-only. Infer before you ask. Collect:
- existing `CLAUDE.md`, `AGENTS.md`, `.claude/` (skills, agents, settings)
- `git branch -a`, `git log --oneline -30` → default/dev branch, branch naming, commit style
- `gh pr list --state merged -L 10` if `gh` works → PR target and title style
- package/build files and CI configs → install, dev, test, lint commands
- ticket keys in branches/commits → which tracker is in use
- sibling folders that are git repos → multi-repo?
- `~/.claude/CLAUDE.md` and `~/.claude/model-reference.md` → global rules already present?

Use the `scout` approach for this (cheap subagent, short report) if the project is
large. Summarise what you found in a short list for the user.

## 4. Grill the user
One question at a time, each with your recommended answer. Confirm what the audit
inferred; ask only about gaps and conflicts. Cover:
1. **What to adopt**: everything, or a subset (workflow agreement, tickets, skills,
   agent routing, home pattern, handover). Partial adoption is fine.
2. **Tracker**: Jira (Atlassian MCP), GitHub Issues (`gh`), or local files.
   Read the matching `docs/trackers/*.md`.
3. **Branching**: dev branch + PRs (which branch? naming?), or commit to `main`.
   Default if the user has no preference: commit to `main` for a solo project,
   `<TICKET>-<slug>` + PR for a team.
4. **Commits and PRs**: commit style; who merges (default: the user).
5. **Tests and verification**: test command; is test-first required; how is
   user-facing behaviour verified (manual, e2e tool, browser).
6. **Repo shape**: single repo, monorepo, or several repos (→ home folder,
   `docs/home-pattern.md`).
7. **Models**: which models their subscription allows for subagents, and any that
   must not be used (e.g. ones billed as extra credits).
8. **Global rules**: install the orchestrator rules in `~/.claude/` too, or keep
   everything project-local.
9. **Language** for the project docs and tickets.

Stop when every placeholder in the templates has an answer.

## 5. Present the plan
List every file you will create or change, with one line each, for example:
```
create  CLAUDE.md                         (from scaffold/CLAUDE.md, filled)
append  CLAUDE.md                         (existing: add sections X, Y; diff shown)
create  HANDOVER.md                       (bootstrapped from current state)
create  docs/tickets/_TEMPLATE.md         (local tracker)
create  docs/workflow/tracker.md          (from docs/trackers/local-files.md, filled)
create  .claude/agents/scout.md, implementer.md
create  .claude/skills/handover/SKILL.md
create  ~/.claude/model-reference.md      (global, filled with their models)
install mattpocock skills plugin          (user runs /plugin; see docs/skills.md)
```
Wait for approval. Adjust and re-present if asked.

## 6. Install
- Copy templates from `scaffold/` (and `scaffold-home/`, `scaffold-global/` if
  chosen); replace every `{{PLACEHOLDER}}`; delete the guidance HTML comments.
- `docs/workflow/tracker.md` = the chosen `docs/trackers/<tracker>.md`, filled with
  their project key, labels, and paths.
- **Never overwrite** an existing file. Merge: append the new sections, keep
  existing rules, and show the diff before writing. Existing skills or agents with
  the same name: keep theirs, report the clash.
- Global files in `~/.claude/`: same rule; append, never replace.
- Skills: tell the user how to install the Matt Pocock collection (it needs their
  action in the `/plugin` UI). After install, map installed skill names onto the
  four roles (grill, spec, tickets, implement) and write the mapping into
  `CLAUDE.md`.
- Tracker setup: Jira → help them connect the Atlassian MCP; GitHub → check
  `gh auth status`, create the labels; local → create `docs/tickets/`.
- Adopt mode: fill `HANDOVER.md` from the audit (open branches,
  uncommitted work, open PRs or tickets).
- Commit the setup following the branching rule just agreed (as its own PR if PRs
  are used). Ask before committing.

## 7. Verify with a dry run
Ask the user for one small real task (or suggest one from the backlog). Walk it
through the flow up to an approved plan: describe → grill → spec → ticket(s).
Stop there unless the user wants to continue into implementation.
Then confirm:
- a fresh session would find `HANDOVER.md` via `CLAUDE.md`;
- the ticket exists in the chosen tracker with dated decisions;
- `handover` and `continue` are explained to the user in one line each.

## 8. Report
Finish with: what was installed, what was skipped and why, and the three phrases
the user will use most: `/grill-me`, `handover`, `continue`.
