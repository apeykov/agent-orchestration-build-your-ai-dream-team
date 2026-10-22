# Project Pulse Implementation Plan

## Goal

Build Mona's Project Pulse dashboard: a small static, card-based app that shows active projects, owners, status, recent activity, priority or risk, and a short contributor-friendly summary. It must run from the VS Code **Run Project Pulse Dashboard** launch configuration and open the real dashboard (`index.html`), not a directory listing.

## Repository findings

- The repository has no `app/` directory. `.vscode/` contains `tasks.json`; create `launch.json` without overwriting it.
- This is a framework-free static app. The devcontainer provides `python3`, so Python's `http.server` will serve the dashboard.
- Loading `project-data.json` with `fetch` requires HTTP rather than `file://`.
- The Designer owns UX and CSS hooks; the Coder owns application logic and runnable-app support. No agent performs Git operations.

## File assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/project-data.json` | Coder | Valid JSON with a top-level `projects` array. Each project has `name`, `owner`, `status`, `recentActivity`, `priority`, and optionally `summary`. |
| `app/styles.css` | Designer | `.dashboard`, `.project-card`, status badge and priority styles, responsive grid, `border-radius`, `box-shadow`, focus, contrast, and reduced-motion rules. |
| `app/index.html` | Coder, using the Designer's markup and class contract | A Project Pulse page that links `styles.css`, fetches `project-data.json`, renders one `.project-card` per project, and provides loading, empty, and error states. |
| `.vscode/launch.json` | Coder | Strict JSON with no comments. A `Run Project Pulse Dashboard` configuration serving `${workspaceFolder}/app` and opening `index.html`. |

## Responsibilities

### Designer

- Define the information hierarchy: header, optional summary, and card grid. Card order: name, status badge, owner, priority, recent activity, and summary.
- Define the CSS hook contract and implement `app/styles.css`.
- Ensure labels do not rely on color alone, WCAG AA contrast, visible keyboard focus, `prefers-reduced-motion`, and a responsive layout from 320px upward.

### Coder

- Write five or six realistic sample projects with mixed statuses and priorities in `app/project-data.json`.
- Write semantic HTML in `app/index.html`, with a `Project Pulse` title, `lang`, viewport metadata, and safe DOM rendering with `textContent`, not data-driven `innerHTML`.
- Fetch data with a relative path and show clear loading, empty, malformed-data, and fetch-error states.
- Create `.vscode/launch.json` using `python3 -m http.server 5500`, `${workspaceFolder}/app` as `cwd`, and `serverReadyAction` opening `http://localhost:%s/index.html`.

## CSS contract

- `.dashboard` is the main container; `.project-card` is an `article` for each project.
- Suggested sub-hooks are `.project-card__name`, `.project-card__owner`, `.status-badge`, `.priority`, `.project-card__activity`, and `.project-card__summary`.
- Status and priority modifiers use lowercase, hyphenated values. Unknown values get a neutral style.
- Supported status vocabulary: `Active`, `On Track`, `At Risk`, `Blocked`, `Paused`, and `Completed`; priority vocabulary: `High`, `Medium`, and `Low`.

## Dependencies

- `app/index.html` depends on the field names and vocabularies in `app/project-data.json`.
- `app/index.html` depends on the Designer's markup and class contract.
- `app/styles.css` depends only on the agreed class contract.
- `.vscode/launch.json` can be written independently, but its final run test depends on the `app/` directory and `index.html`.
- External dependencies are `python3` on PATH and port 5500 being free. There are no npm, framework, CDN, or other runtime dependencies.

## Phases and parallel work decisions

1. **Phase 0 — Contract (sequential):** Orchestrator confirms fields, vocabularies, and CSS contract. This is the prerequisite for all implementation.
2. **Phase 1 — Parallel build:** Designer creates `app/styles.css`; Coder creates `app/project-data.json` and `.vscode/launch.json`. These tasks have no shared files.
3. **Phase 2 — Page assembly (sequential):** After the data and markup contract are ready, Coder creates `app/index.html` and uses the agreed classes.
4. **Phase 3 — Integration and validation (sequential):** Orchestrator runs checks, routes corrections to the file owner, and revalidates. Do not work in parallel on the same file.

## Edge cases

- If fetch fails, data is malformed, or `projects` is missing/not an array, show a visible, friendly error telling users to run the launch configuration.
- Handle an empty array, missing values, unknown status/priority, long text, and script-like data safely; never display `undefined` or inject data as HTML.
- At 320px, 768px, and 1280px, cards must reflow without overflow. Support keyboard navigation and 200% zoom.
- `launch.json` must have neither comments nor trailing commas. Do not alter `.vscode/tasks.json`. If port 5500 is unavailable, report it rather than silently changing the port.

## Validation expectations

1. `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json` succeed.
2. The data file has a top-level `projects` key; each entry has `name`, `owner`, `status`, `recentActivity`, and `priority`.
3. `app/index.html` includes `Project Pulse`, `styles.css`, `project-data.json`, `project-card`, `status`, `recentActivity`, and `priority`.
4. `app/styles.css` includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
5. `.vscode/launch.json` includes `Run Project Pulse Dashboard`, `cwd` `${workspaceFolder}/app`, and a `serverReadyAction` opening `http://localhost:%s/index.html`.
6. Manually run **Run Project Pulse Dashboard**. It must open the dashboard rather than a directory listing; verify cards, error handling, responsive layouts, keyboard focus, and contrast. Stop the server after preview.

## Open questions

- Confirm that `node-terminal` and `serverReadyAction` detect Python's server output in this environment; if not, adjust the pattern or use a pre-launch task.
- The plan assumes fictional sample projects and treats `summary` as optional. Dark mode, filtering, and sorting are out of scope unless requested.
