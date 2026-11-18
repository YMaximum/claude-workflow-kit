# {{PROJECT_NAME}}

{{ONE_LINE_DESCRIPTION}}

## Commands
- {{INSTALL_COMMAND}}: install
- {{DEV_COMMAND}}: run locally
- {{TEST_COMMAND}}: tests (must pass before every commit)
- {{LINT_COMMAND}}: lint / typecheck

## Session start
1. Read `HANDOVER.md`. Continue from there when the user says "continue".
2. Keep `HANDOVER.md` current after every commit and every subagent result.
   When the user says "handover", use the `handover` skill and stop.

## Workflow agreement (agreed with the user on {{DATE}})
How we work. Change it only by agreement, and date the change.

### Discuss first
- The user describes the goal and the expected outcome; you propose the solution.
- Grill (ask one question at a time until nothing is unclear) whenever a decision
  is needed: before implementation, when implementation hits a question, and
  after the user's manual review lists fixes. Never guess a product decision.
- Plan before code: no code changes until the plan is agreed.
- Record every decision in the ticket, dated.

### Tickets
- Tracker: {{TRACKER}}. See `docs/workflow/tracker.md`.
- Pipeline: grill → spec (`/to-spec`) → tickets (`/to-tickets`) → implement.
- Read the whole ticket (description, comments, linked work) before starting.

### Branches, commits, PRs
- Branching: {{BRANCHING_RULE}}
  <!-- e.g. "Branch <TICKET-ID>-<kebab-slug> from `develop`; never commit to `main`."
       or   "Commit directly to `main` (solo project)." -->
- Commits: {{COMMIT_STYLE}}. The message explains *why*, not only what.
- PRs: {{PR_RULE}}. The PR body must stand alone for a reader with no session
  context: what changed, why, how it was verified, what to check by hand.
- The user merges. Never merge, force-push, or rewrite shared history.

### Tests and verification
- {{TEST_RULE}}
  <!-- e.g. "Tests are mandatory for logic. A regression test must be shown failing
       without the fix before the fix is applied." -->
- Verify behaviour yourself before handing over ({{VERIFY_METHOD}}).
- Report real command output. Never call something green you did not run.

### Docs
- Docs change in the same piece of work as the behaviour they describe.

### Agent routing
- You are the orchestrator: keep decisions, design, synthesis and final review.
- Delegate wide, cheap-to-check work to subagents (`.claude/agents/`):
  `scout` (read-only search, cheapest model), `implementer` (bounded edits from a
  clear spec). Use a mid-tier model per call when the spec leaves room for judgement.
- Never delegate: commits, pushes, PR bodies, tracker writes, migrations,
  auth/secrets, or questions for the user.
- Verify every subagent result that feeds a decision. Escalate one model tier on a
  thin result; do not retry the same model with the same brief.

## Adopted from claude-workflow-kit
{{ADOPTED_PARTS}}
<!-- e.g. "workflow agreement, tickets (local), handover, agent routing" -->
