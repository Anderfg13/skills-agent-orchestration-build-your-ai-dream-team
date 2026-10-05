# Agent Team for Mona's Project Pulse Dashboard

The custom agents live in `.github/agents/`. The Orchestrator coordinates three specialists and does not implement anything itself.

| Agent | Model | Responsibility | Agent file |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 | Breaks requests into phases, delegates to specialists with explicit file scopes, verifies the integrated result, and reports to the learner. Does not implement. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 | Researches the repo and docs, then returns a plan with steps, file assignments, dependencies, edge cases, validation expectations, and open questions. Writes no code. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 | Implements logic and fixes bugs. Also creates assigned support files such as `.vscode/launch.json`. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro | Handles UI/UX, accessibility, information hierarchy, responsive layout, and visual styling. | `.github/agents/designer.agent.md` |

## How the team will build Project Pulse

1. The Orchestrator asks the Planner for a plan.
2. The Planner returns ordered steps, file assignments, dependencies, and which work can run in parallel or must run sequentially.
3. The Orchestrator turns the plan into phases and gives each specialist an explicit file scope.
4. Tasks run in parallel only when file scopes don't overlap and there are no data dependencies. Otherwise they run sequentially.
5. The Coder builds the app logic and `.vscode/launch.json` (strict JSON, `cwd` set to `${workspaceFolder}/app`, opening `index.html`).
6. The Designer styles the dashboard with project cards, status badges, clear priority treatment, and a responsive layout, using the `.dashboard` and `.project-card` CSS hooks.
7. The Orchestrator summarizes progress after each phase, surfaces blockers, checks that the result hangs together, and reports the outcome.

## Shared rules

- Each agent stays within the files the Orchestrator assigns.
- No agent stages, commits, or pushes. The learner controls git.
