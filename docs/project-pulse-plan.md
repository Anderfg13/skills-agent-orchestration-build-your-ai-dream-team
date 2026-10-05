# Project Pulse Implementation Plan

## Goal

Build a lightweight, static Project Pulse dashboard for Mona's team. A contributor should be able to see at a glance which projects are active, who owns each one, its status, recent activity, priority or risk, and a short summary. The dashboard is a small static app in `app/`, runnable from the VS Code **Run Project Pulse Dashboard** launch configuration, and it must open `app/index.html`, not a directory listing.

Source of requirements: `.github/project-pulse-brief.md`.

## Summary

Four files make up the deliverable. `app/project-data.json` is the shared contract that both the page and the styling depend on, so it is defined first. After that, the Designer and Coder can work in parallel because their file scopes don't overlap. A final sequential phase integrates and validates.

## File assignments

| File | Owner | Purpose |
| --- | --- | --- |
| `app/project-data.json` | Coder | Top-level `projects` array. Each project has `name`, `owner`, `status`, `recentActivity`, `priority`. |
| `app/index.html` | Coder | Page structure and the script that loads `project-data.json` and renders one `.project-card` per project inside `.dashboard`. Links `styles.css`. |
| `app/styles.css` | Designer | Visual design: layout, cards, status badges, priority treatment, typography, responsive behavior. |
| `.vscode/launch.json` | Coder | Strict-JSON (no comments) launch configuration named **Run Project Pulse Dashboard**, `cwd` set to `${workspaceFolder}/app`, opening `index.html`. |

The Orchestrator may assign each file to only one agent per phase so scopes never overlap.

## Responsibilities

### Designer

- Define the information hierarchy: page title, summary strip, then project cards.
- Style `.dashboard` and `.project-card` (deterministic hooks the Coder's markup must use).
- Style status badges and priority treatment with clear contrast, so meaning isn't carried by color alone.
- Use rounded corners, shadows, readable spacing, and clear typography.
- Make the layout responsive from phone to desktop.
- Cover accessibility: color contrast, focus states, readable text sizes.
- Own `app/styles.css` only. Report design decisions and any class names the Coder must use.

### Coder

- Create `app/project-data.json` with realistic sample projects covering several statuses and priorities.
- Create `app/index.html` with semantic markup that references `styles.css` and `project-data.json`.
- Render cards from the JSON, using the agreed CSS hooks and escaping/inserting text safely (`textContent`, not `innerHTML`).
- Handle errors explicitly: show a visible message if the JSON fails to load or the `projects` array is missing or empty.
- Create `.vscode/launch.json` so the app is served from `app/` and opens `index.html`.
- Do not edit `app/styles.css`. Report what changed, what was validated, and remaining risks.

### Orchestrator

- Sequence the phases below, give each agent an explicit file scope, and summarize after each phase.
- Verify the integrated result and report the outcome. It does not implement.

## Implementation phases

### Phase 1: Contract (sequential)

1. The Coder creates `app/project-data.json` with the agreed schema.
2. The Orchestrator confirms the CSS hook names (`.dashboard`, `.project-card`, plus badge classes for status and priority) and passes them to both agents.

### Phase 2: Build (parallel)

- **Coder:** `app/index.html` and `.vscode/launch.json`.
- **Designer:** `app/styles.css`.

### Phase 3: Integrate and validate (sequential)

1. The Orchestrator checks that the markup classes match the stylesheet selectors.
2. The Coder fixes any markup or data mismatches. The Designer fixes any styling mismatches, one agent at a time.
3. Run the dashboard from the launch configuration and review it.

## Dependencies

- `app/index.html` depends on `app/project-data.json` (it fetches and renders it) and on the field names in the schema.
- `app/styles.css` depends on the class names that `app/index.html` emits. It doesn't depend on the data file content, only on the agreed hooks.
- `.vscode/launch.json` depends on `app/index.html` existing, because it opens that page, and on `app/` being the served root.
- Final validation depends on all four files.

## Parallel work decisions

| Work | Decision | Reason |
| --- | --- | --- |
| `app/project-data.json` | Sequential, first | Both the page and the review depend on its schema. |
| `app/index.html` + `.vscode/launch.json` (Coder) alongside `app/styles.css` (Designer) | **Parallel** | Disjoint file scopes. The only shared dependency is the CSS hook names, which are fixed in Phase 1. |
| Integration fixes | Sequential | Fixes may touch overlapping files, so one agent edits at a time. |
| Validation run | Sequential, last | Needs every file complete. |

## Edge cases to handle

- `fetch` of `project-data.json` fails when the page is opened as a `file://` URL. This is why the launch configuration must serve `app/` over HTTP.
- JSON load error, malformed JSON, missing `projects` key, or an empty array: show an explicit message, not a blank page.
- A project with a missing field or an unexpected `status` or `priority` value: render a neutral fallback badge.
- Long project names or activity text must wrap without breaking the card layout.
- Narrow viewports: cards stack to one column.

## Validation expectations

- All four files exist at the paths above.
- `app/project-data.json` parses as valid JSON, has a top-level `projects` array, and every project has `name`, `owner`, `status`, `recentActivity`, `priority`.
- `app/index.html` includes "Project Pulse", references `styles.css` and `project-data.json`, and renders `.dashboard` and `.project-card`.
- `app/styles.css` defines `.dashboard` and `.project-card` and styles status badges and priority.
- `.vscode/launch.json` is valid strict JSON, includes **Run Project Pulse Dashboard**, has `cwd` set to `${workspaceFolder}/app`, and opens `index.html`.
- Launching the configuration shows the dashboard UI, not a directory listing.
- Every project in the JSON appears as a card with its owner, status, activity, and priority.
- The layout is checked at desktop and phone widths, and the error state is checked by temporarily breaking the data path.
- No agent stages, commits, or pushes. The learner controls git.

## Open questions

- Which port or static server should the launch configuration use? Choose one deterministic command and port, and record it in the Coder's report.
- Are the sample project names and owners placeholders, or should Mona supply real data?
- What status and priority vocabularies should be used? Suggested defaults: status `On track`, `At risk`, `Blocked`, `Done`; priority `High`, `Medium`, `Low`.
