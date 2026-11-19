# {{PROJECT_NAME}}

## About this app
{{APP_CONTEXT}}
<!-- 3-5 lines agreed with the user: what it does, for whom, the main jobs users do,
     and where it is heading. Details: docs/codebase/README.md -->
Key terms: {{KEY_TERMS}} (all terms: `docs/codebase/glossary.md`)

## Commands
- {{INSTALL_COMMAND}}: install
- {{DEV_COMMAND}}: run locally
- {{TEST_COMMAND}}: tests (must pass before every commit)
- {{LINT_COMMAND}}: lint / typecheck

## Session start
1. Read `HANDOVER.md` if present. Continue from there when the user says "continue".
   (Started from a multi-repo home folder? Use the home `HANDOVER.md` instead.)
2. Keep `HANDOVER.md` current after every commit and every subagent result.
   When the user says "handover", use the `handover` skill and stop.

## Always (agreed with the user on {{DATE}})
1. The user describes the goal and expected outcome; you propose the solution.
2. Plan before code: no code changes until the plan is agreed.
3. When a decision is needed, grill: questions with a recommended answer each.
   Batch independent questions (up to ~4); ask a question that depends on an
   earlier answer only after that answer. Never guess a product decision.
4. Record every decision in the ticket ({{TRACKER}}), dated.
5. Branching: {{BRANCHING_RULE}}. The user merges; never force-push.
6. Tests: {{TEST_RULE}}
7. Before handing over: {{VERIFY_METHOD}} Report real output only.
8. Docs and the codebase map change in the same commit as the code they describe.
9. Never delegate commits, pushes, PR bodies, tracker writes, migrations,
   auth/secrets, or questions for the user.

## Read when relevant
| When you are... | Read first |
|---|---|
| starting or planning any ticket | `docs/claude/workflow.md` |
| delegating to subagents | `docs/claude/agents.md` |
| guiding the user, or they seem unsure | `docs/claude/coaching.md` |
| touching code you have not mapped yet | `docs/codebase/README.md` |
{{AREA_ROUTES}}
<!-- one row per mapped area: | working on <area> (<paths>) | `docs/codebase/<area>.md` |
     plus, if they exist: business rules → docs/codebase/domain-rules.md,
     writing code → docs/claude/coding-style.md. Drop rows for files not created.
     Unmapped area touched by a ticket: create its file from
     docs/codebase/_AREA_TEMPLATE.md in the same change and add a row here. -->

## Gotchas
{{GOTCHAS}}
<!-- the top 3-5 only: critical flows that must not break, no-go folders (the rule
     lives here, not as a file inside them), surprising commands. -->
{{ADOPTED_FOOTER}}
