# Project Pulse Final Handoff

## Final result

Project Pulse is a small static dashboard for Mona's team. It shows each project's name, owner, status, recent activity, and priority as a card with status and priority badges. The page loads its data from `app/project-data.json` and renders it in the browser. It runs from the **Run Project Pulse Dashboard** launch configuration and opens the dashboard page, not a directory listing.

## Agents

The agent team is defined in `.github/agents/` and documented in `docs/agent-team.md`.

- **Orchestrator:** coordinates the work, assigns file scopes, and reports the outcome. It does not implement.
- **Planner:** produced the implementation plan in `docs/project-pulse-plan.md`, covering phases, file ownership, dependencies, parallel work, and validation expectations.
- **Designer:** owns the visual and accessibility direction, which is implemented in `app/styles.css`: card layout, rounded corners, shadows, status and priority badges, responsive grid, focus states, and reduced-motion support.
- **Coder:** owns the implementation of `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.

## How the plan was used

`docs/project-pulse-plan.md` set the file assignments and the order of work. The data schema came first, then the page and the styling, which have disjoint file scopes, then integration and checks. The build followed those phases and file assignments.

## App files created

| File | Contents |
| --- | --- |
| `app/index.html` | Page titled "Project Pulse". Links `styles.css`, fetches `project-data.json`, and renders one `project-card` per project with status, `recentActivity`, and priority. Shows a visible error message if the data can't be loaded. |
| `app/styles.css` | `.dashboard` grid, `.project-card` styling with `border-radius` and `box-shadow`, badge colors, responsive layout. |
| `app/project-data.json` | Top-level `projects` array with five projects. Each has `name`, `owner`, `status`, `recentActivity`, `priority`. |

## Launch configuration

The launch file is `.vscode/launch.json`. It is strict JSON with no comments and defines **Run Project Pulse Dashboard**. The configuration runs `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`. A `serverReadyAction` opens `http://localhost:%s/index.html`.

## validation

Checked by inspecting the files and parsing the JSON:

- `app/index.html` contains "Project Pulse", links `styles.css`, references `project-data.json`, and builds cards with the `project-card` class.
- `app/styles.css` includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- `app/project-data.json` parses as JSON, has a top-level `projects` key, and all five projects include `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json` parses as JSON, includes **Run Project Pulse Dashboard**, serves from `app/`, and opens `http://localhost:%s/index.html`.

Not yet done: running the launch configuration and viewing the page in a browser. That check is left to the learner (see next steps).

## handoff

### Next steps

1. Open Run and Debug in VS Code, select **Run Project Pulse Dashboard**, and press play. Confirm the browser shows the Project Pulse dashboard, then stop the server.
2. Replace the sample projects in `app/project-data.json` with Mona's real project data.

### Limitations

- The page must be served over HTTP. Opening `app/index.html` directly as a file will show the data-load error message.
- The launch command uses `python3`. On a machine where only `python` is available, such as some Windows setups, the command needs adjusting.
- The sample data is fictional.
- The visual layout was not reviewed in a browser at desktop and phone widths.
