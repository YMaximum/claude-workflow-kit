# claude-workflow-kit

A structured way to build apps with [Claude Code](https://claude.com/claude-code):
describe the goal, discuss it with Claude (grill), turn decisions into tickets, let
Claude implement, and review. With agent routing to save tokens, a home folder for
multi-repo apps, and handover files so any session can resume with `continue`.

Works for **new projects** and for **existing projects** that don't use it yet.
Doubles as a scaffold for your next project.

## Quick start
Open Claude Code in your project folder and say one of these:

**New project**
> Read https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/SETUP.md and follow it to set up this project.

**Existing project**
> Read https://raw.githubusercontent.com/YMaximum/claude-workflow-kit/main/SETUP.md and adopt this workflow in this existing project.

Claude reads the guide, looks at your project, asks you a few questions (tracker,
branching, tests, models...), shows a plan, and installs only after you approve.
Nothing existing is overwritten.

## What's inside
| Path | What it is |
|---|---|
| [GUIDE.md](GUIDE.md) | the full workflow, for people and Claude |
| [SETUP.md](SETUP.md) | step-by-step instructions **for Claude** to install it |
| [docs/workflow.md](docs/workflow.md) | the end-to-end flow and the three grill points |
| [docs/agent-routing.md](docs/agent-routing.md) | which agent/model does which task |
| [docs/handover.md](docs/handover.md) | checkpoints, `/clear` → `continue` |
| [docs/home-pattern.md](docs/home-pattern.md) | multi-repo home folder vs monorepo |
| [docs/skills.md](docs/skills.md) | Matt Pocock's skills and how to adapt them |
| [docs/trackers/](docs/trackers/) | Jira, GitHub Issues, local files |
| [docs/adopting-existing-project.md](docs/adopting-existing-project.md) | adopt mode |
| [scaffold/](scaffold/) | files copied into a project (`CLAUDE.md`, `HANDOVER.md`, ticket template, agents, handover skill) |
| [scaffold-home/](scaffold-home/) | `CLAUDE.md` for a multi-repo home folder |
| [scaffold-global/](scaffold-global/) | `~/.claude/CLAUDE.md` routing rules + `model-reference.md` |

## Using it as your own scaffold
Clone it once (e.g. to `~/claude-workflow-kit`), then for each project tell Claude:
> Use ~/claude-workflow-kit/SETUP.md to set up this project (skip the clone step).

Improve the kit as you learn: update the scaffold and the observed-results log in
`model-reference.md`, and the next project starts better.

## Credits
Workflow skills (grill-me, to-spec, to-tickets, implement) and the home-folder idea:
[Matt Pocock](https://github.com/mattpocock/skills).

## License
MIT
