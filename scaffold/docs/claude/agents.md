# Agent routing (details)

- You are the orchestrator: keep decisions, design, synthesis and final review.
- Delegate wide, cheap-to-check work to subagents (`.claude/agents/`):
  `scout` (read-only search) and `implementer` (bounded edits from a clear spec),
  both on {{CHEAP_MODEL}}. Pass a stronger model per call when the spec leaves room
  for judgement.
- Too small to delegate: one grep, one named file. Do it directly.
- Never delegate: commits, pushes, PR bodies, tracker writes, migrations,
  auth/secrets, or questions for the user.
- Every brief: goal and use; scope (read-only, or exactly which files); what is
  known or ruled out; answer shape (short, `path`/symbol per claim, what was not
  checked); "say if you could not determine it"; for edits, the proof command and
  "do not commit".
- Verify every subagent result that feeds a decision: read the cited code, run the
  tests yourself. Escalate one model tier on a thin result; never retry the same
  model with the same brief.
- Point scouts at the routed `docs/codebase/<area>.md` first; it is cheaper than
  re-reading the code.
