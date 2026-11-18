# Agent routing: which agent does which task

Goal: spend tokens where judgement is needed and nowhere else. The main session
(strongest model) is the **orchestrator**: it decides, designs, synthesizes and
verifies. Wide, cheap-to-check work goes to **subagents** on the cheapest model
that can do it well.

## The routing table
| Task | Who | Why |
|---|---|---|
| Decisions, design, synthesis, final review, answers to the user | main session | judgement stays in one place |
| Find files, list every use of a symbol, scan logs, collect facts from docs | `scout` (haiku) | wide reading, short answer |
| Mechanical edits from an exact pattern; code/tests from a decided spec | `implementer` (haiku) | bounded, verifiable |
| Same, but the spec leaves choices; code review; multi-file analysis | subagent on sonnet | needs more reasoning |
| Hard design or debugging where a wrong answer is costly | opus, sparingly | prefer the main session |
| Several independent reviews or searches | parallel subagents in one message | wall-clock time |

## When not to delegate
- **Too small:** one grep, one named file. Delegation has a fixed cost (the brief,
  the subagent's start-up reading, checking its result). Do it directly.
- **Needs the conversation:** design, root-causing a non-obvious bug, anything
  where what was said matters more than what is in the files.
- **Never:** commits, pushes, merges, PR bodies and other evidence, tracker writes,
  migrations, auth and secrets, queries against real data, questions for the user.

## Writing a brief
A subagent knows nothing of the conversation. Every brief has:
1. the goal and what the answer is for;
2. scope: where to look; read-only, or exactly which files may change;
3. what is already known or ruled out;
4. answer shape: short, `file:line` per claim, plus what was **not** checked;
5. "say you could not determine it rather than guess";
6. for edits: the acceptance command, and "do not commit".

## Verifying
- A subagent's claim is a lead, not a fact. Read the cited lines.
- For a negative the decision depends on ("X is never called"), run one grep yourself.
- Read the full diff and run the tests yourself. Never repeat "tests pass" unrun.

## Escalating
- Thin, inconsistent, or obviously incomplete: retry **one tier up**
  (haiku → sonnet → opus) or do it yourself. Never the same model with the same brief.
- After two failed delegations on the same task, do it directly.
- Follow-ups go to the same agent (it keeps its context).

## Keeping it honest
- Keep `~/.claude/model-reference.md` (template in `scaffold-global/`) with the allowed
  models and an **observed results** log: one line per delegation that went
  surprisingly well or badly. Choose models from evidence, not habit.
- Read numbers from command output, not screenshots: images cost tokens on every
  later turn.
- Bound command output before it enters the context (`tail`, counts, filters).
