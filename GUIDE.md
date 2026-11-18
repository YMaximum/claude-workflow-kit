# Building apps with Claude Code: the guide

This is the full workflow. It is written for both people and Claude: a Claude
session can read this file and the linked `docs/` and work this way. To install the
workflow in a project, follow [SETUP.md](SETUP.md).

Contents
1. [Principles](#1-principles)
2. [The workflow agreement](#2-the-workflow-agreement)
3. [Skills](#3-skills)
4. [The end-to-end flow](#4-the-end-to-end-flow)
5. [Tickets are the source of truth](#5-tickets-are-the-source-of-truth)
6. [Agent routing](#6-agent-routing)
7. [Multi-repo: the home pattern](#7-multi-repo-the-home-pattern)
8. [Handover and checkpoints](#8-handover-and-checkpoints)
9. [Cheat sheet](#9-cheat-sheet)

---

## 1. Principles
- **Describe the goal, not the details.** Say what you want and what "done" looks
  like. Current models design solid solutions; implementation details come up as
  questions, not as your homework.
- **Always discuss.** Decisions are made together and written down. Claude never
  guesses a product decision; the user never has to remember one.
- **Claude implements, the user reviews.** Claude writes code, tests, commits and
  PRs. The user reviews, tests by hand and merges.
- **Everything leaves a trace.** Decisions in tickets, progress in handover files,
  changes in PRs. Any session, human or Claude, can pick up from the trace.
- **Spend tokens on judgement.** The strongest model orchestrates; cheap models do
  the wide reading.

## 2. The workflow agreement
Written once in the project's `CLAUDE.md` (Claude reads it at every session start),
so the rules never need repeating. Template: [`scaffold/CLAUDE.md`](scaffold/CLAUDE.md).

It settles:
| Topic | Example |
|---|---|
| Tracker | Jira (MCP), GitHub Issues (`gh`), or local files |
| Branching | `<TICKET>-<slug>` from `develop`, PR back to `develop`; or commit to `main` if nothing else is agreed |
| Commits | conventional commits; message explains *why* |
| PRs | body stands alone; the user merges |
| Tests | mandatory for logic; regression test shown failing before the fix |
| Verification | manual or e2e check before handing over |
| Docs | updated in the same change |
| Agent routing | which work is delegated, to which model |

Build it with a grill: ask Claude to interview you about how you want to work, then
write the answers into `CLAUDE.md`. Date it; change it only by agreement.

## 3. Skills
Matt Pocock's skill collection gives the four workflow steps a standard form:
grill-me (interview), to-spec, to-tickets, implement. Use them as they are, or copy
and adapt them into `.claude/skills/` so the team shares one version.
Details: [docs/skills.md](docs/skills.md).

## 4. The end-to-end flow
```
Describe ─▶ Grill #1 ─▶ Spec ─▶ Tickets ─▶ Implement ─▶ Commit/PR ─▶ Grill #2 ─▶ Update ─▶ Manual review
                                              ▲                                                │
                                              └──── fix tickets ◀── Grill #3 ◀── list fixes ◀──┤
                                                                                     merge ◀───┘
```
1. **Describe** the goal and expected outcome.
2. **Grill #1** in plan mode: Claude asks one question at a time until scope,
   behaviour and edge cases are clear. No files change in plan mode.
3. **Spec**: the decisions written up (`/to-spec`).
4. **Tickets**: vertical slices, each demoable and sized for one fresh session,
   shown as a numbered list for approval before creation (`/to-tickets`).
5. **Implement**: fresh session reads the whole ticket; branch first; failing test
   first; cheap subagents for wide work; full checks; docs updated.
6. **Commit and PR** per the agreement (commit to `main` and skip the PR only if
   nothing was agreed and the repo has no existing branching convention).
7. **Grill #2**: "grill me on the questions you have from implementing this
   ticket." Claude asks instead of guessing; answers go into the ticket.
8. **Update the branch** with the decisions.
9. **Manual review and test** by the user. Fixes found? List them all, then
   **Grill #3**: "here is my list of fixes; grill me before fixing them." They
   become fix tickets and go back to step 5. Nothing to fix? The user merges.

A grill is a tool, not a phase: start one any time a decision is needed.
Details: [docs/workflow.md](docs/workflow.md).

## 5. Tickets are the source of truth
Sessions are temporary; tickets are permanent. Any tool works if decisions and
progress can be traced later.

| Tracker | Fits | Guide |
|---|---|---|
| Jira via Atlassian MCP | teams already in Jira | [docs/trackers/jira.md](docs/trackers/jira.md) |
| GitHub Issues via `gh` | code on GitHub | [docs/trackers/github-issues.md](docs/trackers/github-issues.md) |
| Local Markdown in `docs/tickets/` | solo / small teams, zero setup | [docs/trackers/local-files.md](docs/trackers/local-files.md) |

Every ticket has at least: goal and expected outcome, testable acceptance criteria,
out of scope, **dated decisions** from grills, status and PR link.
Template: [`scaffold/docs/tickets/_TEMPLATE.md`](scaffold/docs/tickets/_TEMPLATE.md).

## 6. Agent routing
The main session (strongest model) orchestrates: decisions, design, synthesis,
final review. Subagents on the cheapest capable model do the wide work.

| Task | Who |
|---|---|
| Decisions, design, synthesis, review, answers to the user | main session |
| Search, scans, extraction, log summaries | `scout` on haiku |
| Mechanical edits, code from a decided spec | `implementer` on haiku |
| Spec leaves choices, code review, multi-file analysis | sonnet |
| Hard, costly-if-wrong problems | opus, sparingly |

Rules: self-contained briefs; short reports with `file:line`; verify before relying;
escalate one tier on a thin result; never delegate commits, pushes, PR bodies,
tracker writes, migrations, auth or questions for the user.
Details: [docs/agent-routing.md](docs/agent-routing.md). Templates:
[`scaffold/.claude/agents/`](scaffold/.claude/agents/),
[`scaffold-global/`](scaffold-global/).

## 7. Multi-repo: the home pattern
If the app spans several repos, start Claude in a parent "home" folder that holds
the clones and a `CLAUDE.md` with the repo map. Claude then sees the whole system;
each repo keeps its own git history, branches and PRs. Or use a monorepo.
Details: [docs/home-pattern.md](docs/home-pattern.md).

## 8. Handover and checkpoints
One file, `HANDOVER.md`, holds the state (branch, uncommitted work), what is in
flight with how to resume each item, and the next steps. It is updated after every
commit. If a usage limit hits or the session is
interrupted, or the context just gets long: `/clear`, then type `continue`.
Saying `handover` makes Claude record state and stop.
Details: [docs/handover.md](docs/handover.md).

## 9. Cheat sheet
| You want to | Say / do |
|---|---|
| Start a feature | describe the goal + expected outcome, then Shift+Tab into plan mode and `/grill-me` |
| Write it down | `/to-spec`, then `/to-tickets` |
| Build a ticket | "implement APP-012" (or `/implement`) |
| Claude is unsure mid-way | "grill me on your open questions" |
| After your review | list the fixes, "grill me on these before fixing" |
| Pause safely | `handover`, then `/clear` |
| Resume | `continue` |
| Adopt in an old project | see [docs/adopting-existing-project.md](docs/adopting-existing-project.md) |
