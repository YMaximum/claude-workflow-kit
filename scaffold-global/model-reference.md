---
name: model-reference
description: Models allowed for subagents, which task goes to which model, effort and escalation rules, and a log of observed results. Read before choosing a subagent model.
---

Last checked: {{YYYY-MM-DD}}. Source: https://platform.claude.com/docs/en/models/overview

Only models covered by {{your plan}} are allowed. {{List any model that bills extra
credits here as excluded.}}

## Allowed models
| Agent `model` | Use | Status |
|---|---|---|
| `haiku` | cheapest, fastest: scans, extraction, mechanical edits | default for subagents |
| `sonnet` | balanced: implementation from a brief, review, multi-file analysis | allowed |
| `opus` | strongest: the main session; hard problems where a wrong answer is costly | main session; subagents sparingly |

Model names and versions change. Update this table when they do.

## Which model for which task
| Task | Model | Why |
|---|---|---|
| File search, grep-style scans, listing, reading many files for facts | `haiku` | cheap and fast; the orchestrator checks the result |
| Classification, extraction, summarizing a known file or log | `haiku` | matches its purpose |
| Mechanical edits with exact instructions (renames, formatting, boilerplate) | `haiku` | low risk |
| Implementation from a clear spec, writing tests | `haiku` → `sonnet` if the spec leaves choices | |
| Code review, unfamiliar code, multi-file analysis | `sonnet` | needs more reasoning |
| Hard design or debugging where a wrong answer is costly | `opus` | use sparingly; prefer the main session |
| Synthesis, final decisions, user-facing answers | main session | judgement stays in one place |

## Never delegate
Commits, pushes, merges, PR bodies, tracker writes, migrations, auth and secrets,
query construction against real data, questions for the user.

## Effort
| Situation | Effort |
|---|---|
| Plain scan or lookup | low |
| Normal analysis or review | medium (model default) |
| Wrong answer is costly, or a first attempt came back thin | high |

## Escalation
Thin, inconsistent, or obviously incomplete result: retry once on the next tier up
(`haiku` → `sonnet` → `opus`). Never retry the same model with the same prompt.

## Observed results
One line per notable delegation, so model choice improves over time.
Format: `YYYY-MM-DD | model | task | outcome`
- 
