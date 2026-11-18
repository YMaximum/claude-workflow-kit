# Adopting the workflow in an existing project

For a project that already exists but does not follow this pattern yet. The idea:
**fit the agreement to how the project already works**, then fill the gaps. Nothing
existing is overwritten.

Tell Claude, from the project root:
> Read https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/SETUP.md and adopt this workflow in this existing project.

## What Claude does
1. **Audit (read-only).** Infers the current conventions instead of asking:
   - existing `CLAUDE.md`, `AGENTS.md`, `.claude/` (skills, agents, settings)
   - default and dev branches, branch naming (`git branch -a`)
   - commit style (`git log --oneline -30`)
   - PR habits and target branch (`gh pr list --state merged -L 10`, if available)
   - test, lint, build and CI commands (package files, Makefile, CI workflows)
   - ticket keys in branch names and commits → which tracker is in use
   - sibling repos in the parent folder → multi-repo?
2. **Report and grill on gaps.** Shows what it inferred ("branches look like
   `APP-123-slug` from `develop`; commits are conventional"), asks you to confirm,
   and asks only what it could not infer or what conflicts.
3. **Choose what to adopt.** All of it, or a subset, e.g. just handover + agent
   routing. Adoption can be incremental.
4. **Plan, then merge.** Lists every file it will create or change. Existing
   `CLAUDE.md`: new sections are **appended**, shown as a diff, written only after
   approval. Existing skills/agents are kept; name clashes are reported, not replaced.
5. **Bootstrap state.** Writes `HANDOVER.md` from the current
   situation: open branches, uncommitted work, open PRs and tickets. The first
   `continue` works immediately.
6. **Optional extras.** Turn a backlog or TODO list into tickets (`/to-tickets`);
   wrap sibling repos in a home folder (`docs/home-pattern.md`); install the
   global routing rules.
7. **Dry run.** Picks one small real task and walks it to an approved plan.

## What it never does
- Overwrite or delete existing instructions, skills or agents.
- Rename branches, rewrite history, or change CI.
- Commit the setup without your approval (it follows the branching rule it just
  wrote down, so the setup can arrive as its own PR).
