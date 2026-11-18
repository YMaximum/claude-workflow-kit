# Skills

A skill is a packaged instruction set Claude loads on demand, invoked as
`/skill-name` or picked automatically from its description. Skills turn "how we
work" into something every session does the same way.

## The core four (Matt Pocock's collection)
Source: https://github.com/mattpocock/skills. Install it as a Claude Code plugin
by following that repo's README.

| Role in the workflow | Skill (name may differ by version) | Output |
|---|---|---|
| Interview the user until nothing is unclear | `grill-me` / `grilling` | agreed decisions |
| Turn the discussion into a spec | `to-spec` / `to-prd` | spec document |
| Split the spec into vertical-slice tickets | `to-tickets` / `to-issues` | tickets in the tracker |
| Work one ticket end to end | `implement` / `tdd` | branch, commits, PR |

The collection is updated over time and names change. After installing, list the
installed skills and map them onto the four roles above; record the mapping in the
project's `CLAUDE.md`. The collection also has useful extras (code review, diagnosing
bugs, prototyping, domain modelling).

## Adapting them
Use them as they are, or copy one into `.claude/skills/<name>/SKILL.md` in the repo
and change it, for example:
- `to-tickets` writing your exact ticket template, labels and tracker;
- `implement` enforcing your branch naming, test gate and PR body format.

A project skill in `.claude/skills/` is committed with the repo, so the whole team
runs the same version. Keep the original as the reference and note what you changed
at the top of your copy.

## Included in this kit
- `scaffold/.claude/skills/handover/`: record in-flight state and stop, so the next
  session resumes with `continue`.
