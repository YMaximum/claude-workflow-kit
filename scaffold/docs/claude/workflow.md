# Workflow agreement (details)

Agreed with the user on {{DATE}}. The short version is the "Always" list in
`CLAUDE.md`; this file has the details. Change it only by agreement, and date it.

## Discuss first
- The user describes the goal and the expected outcome; you propose the solution.
- Grill whenever a decision is needed: before implementation, when implementation
  hits a question, and after the user's manual review lists fixes.
- How to grill: give each question a recommended answer. Batch questions that are
  independent of each other (up to ~4 per message). Never put a question in the
  same batch as the answer it depends on; ask it after. This overrides "one
  question at a time" in any installed grill skill.
- Plan before code. Record every decision in the ticket, dated.

## Tickets
- Tracker: {{TRACKER}}. See `docs/tracker.md`.
- Pipeline: grill → spec → tickets → implement.
- Specs: {{SPEC_LOCATION}}
- Read the whole ticket (description, comments, linked work) before starting.
- Before touching an area, read its `docs/codebase/<area>.md` (routed from
  `CLAUDE.md`). Unmapped area: map it in the same change.

## Skills
| Role | Skill | If unavailable |
|---|---|---|
| grill | {{GRILL_SKILL}} | questions with recommended answers, batched as above, until nothing is unclear |
| spec | {{SPEC_SKILL}} | write goal, scope and non-goals, behaviour, acceptance criteria, risks |
| tickets | {{TICKETS_SKILL}} | vertical slices, each demoable in one session; numbered list for approval first |
| implement | {{IMPLEMENT_SKILL}} | read ticket → test first → implement → checks → commit per this agreement |

## Branches, commits, PRs
- Branching: {{BRANCHING_RULE}}
- Commits: {{COMMIT_STYLE}}. The message explains *why*, not only what.
- PRs: {{PR_RULE}}. The PR body must stand alone for a reader with no session
  context: what changed, why, how it was verified, what to check by hand.
- The user merges. Never merge, force-push, or rewrite shared history.

## Tests and verification
- {{TEST_RULE}}
- Before handing over: {{VERIFY_METHOD}}
- Report real command output. Never call something green you did not run.

## Docs and the codebase map
- Docs change in the same piece of work as the behaviour they describe.
- A change that adds, moves or removes a module updates its area file in the same
  commit. Review asks: "is the map still true?"
- Found a wrong statement in the map while working? Fix it and say so.
