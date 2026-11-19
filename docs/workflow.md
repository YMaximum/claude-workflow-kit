# The end-to-end workflow

From an idea to a merged change. The user decides and reviews; Claude asks,
plans, implements and reports. Discussion happens at every decision point.

```
 1 Describe goal ─▶ 2 Grill #1 (plan mode) ─▶ 3 Spec ─▶ 4 Tickets ─▶ 5 Implement
                                                                         │
 9 Manual review/test ◀─ 8 Update branch ◀─ 7 Grill #2 ◀─ 6 Commit + PR ◀┘
        │
        ├─ fixes found ─▶ list them ─▶ Grill #3 ─▶ fix tickets ─▶ back to 5
        └─ all good ────▶ user merges
```

## 1. Describe the goal (user)
Say what you want and what "done" looks like. Leave out implementation details;
they come up as questions.

> "Users should be able to export the monthly report to Excel. Expected: an Export
> button on the report page; the file follows the active filters."

## 2. Grill #1, in plan mode (together)
Plan mode (Shift+Tab in Claude Code) lets Claude read and ask, but not change files.
Run the grill skill: Claude asks questions, each with its recommended answer, until
nothing is unclear: scope, behaviour, edge cases, non-goals, constraints. Claude
explores the code itself instead of asking things the code can answer.

**Batching rule:** questions that are independent of each other go together (up to
~4 per message). A question whose options depend on an earlier answer is never in
the same batch; it comes after that answer. This overrides "one question at a
time" in installed grill skills.

## 3. Spec (Claude: `/to-spec`)
The agreed decisions become a spec: problem, goal, scope and non-goals, behaviour,
acceptance criteria, risks. Stored where the tracker says (a doc in the repo, a Jira
epic description, or a parent GitHub issue).

## 4. Tickets (Claude: `/to-tickets`)
- Slice **vertically**: each ticket is a complete, demoable path through every
  layer that fits in one fresh session. Not "backend ticket, then frontend ticket".
- Wide mechanical refactors: expand → migrate in batches (one ticket each) → contract.
- Claude shows the breakdown as a **numbered list for approval** before creating
  anything. Fixing a bad split in conversation is cheaper than in the tracker.
- Each ticket: goal, expected outcome, acceptance criteria (behaviour, never the
  test code), out of scope, decisions, blockers. Label ready ones `ready-for-agent`.
- Do not `/clear` until the tickets exist; then clearing is safe.
- If the user asks to split by layer (backend ticket, UI ticket), accept when each
  part stays demoable on its own (e.g. the API via curl, the UI against a stub),
  and note the split in the tickets.

## 5. Implement (Claude: `/implement` or the ticket loop)
1. Fresh session: read `HANDOVER.md`, then the **whole** ticket
   (description, comments, linked work).
2. Branch per the workflow agreement, before the first commit.
3. Test first where it applies: write the failing test, show it red, then make it
   green. For a bug fix, prove the regression test fails without the fix.
4. Delegate wide reading and bounded edits to cheap subagents
   (`docs/agent-routing.md`); keep design and verification in the main session.
5. Run the full checks; verify behaviour (browser/e2e/CLI) for user-facing changes.
6. Update docs in the same change. Update `HANDOVER.md` after every commit.

## 6. Commit and PR (Claude)
- Commit to the branch the agreement names. Only if there is no agreement and no
  existing convention (e.g. a `develop` branch) commit to `main` and skip the PR.
- Commit messages explain **why**.
- PR body stands alone: what, why, how it was verified (real output), what the
  reviewer should check by hand, merge order if several repos.
- If scope turned out wrong: finish the coherent part, PR it, and open a follow-up
  ticket for the rest. Scaling down is the user's call.

## 7. Grill #2: questions from implementation (together)
Implementation surfaces things nobody saw: a constraint in the code, a wrong
assumption, a new design choice. Claude does not guess. The user says:

> "Grill me on the questions you have from implementing this ticket."

Answers go into the ticket under **Decisions**, dated.

## 8. Update the branch (Claude)
Apply the decisions, re-run checks, push, update the PR body.

## 9. Manual review and test (user)
Run it yourself. Read the PR. Then:
- **Fixes needed**: write them all down as a list, then start **Grill #3**:
  > "Here is my list of fixes from review. Grill me before fixing them."

  The answers become fix tickets (or ticket updates) and go back to step 5.
- **All good**: merge (the user merges, never Claude).

## The rule behind all of it
**Always discuss with Claude.** A grill is not a phase; it is a tool for any moment
a decision is needed. Three points are standard (before, during, after review),
but start one whenever you or Claude is unsure.
