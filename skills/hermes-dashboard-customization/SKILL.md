---
name: hermes-dashboard-customization
description: "Use when adding custom pages or themes to Hermes dashboard."
version: 1.0.0
author: Everlily Nexus
license: MIT
platforms: [android, linux, termux]
metadata:
  hermes:
    tags: [Hermes Dashboard, Custom Themes, Custom Pages, Web Frontend, Studio Modules]
---

# Hermes Dashboard Customization

Upgrade the Hermes Agent dashboard in place with custom Studio modules, themes, pages, and routes. Preserves all existing routes, pages, navigation, APIs, authentication, settings, jobs, tools, skills, memory, sessions, cron functionality, and agent integration.

Installed Hermes version: v0.22.2 (2026.9.24, stable). Official docs: https://hermes-agent.nousresearch.com/docs

## Activation

Reference by name: `hermes-dashboard-customization`.

## When to Use

Use this skill when you need to add custom pages, themes, routes, or Studio-style modules to the Hermes Agent dashboard while preserving all existing functionality — routes, pages, navigation, APIs, auth, settings, jobs, skills, memory, sessions, cron, and agent integration. Typical triggers: building a Cookie Run Studio dashboard, adding a Kanban board, adding a project manager, or registering a custom theme.

## Always-On Rules

1. **Inspect first, never modify blindly.** Perform a read-only inspection of the existing dashboard before changing anything: project directory, frameworks, routes, APIs, storage, Hermes integration, startup command.
2. **Back up before modifying.** Copy every file you will touch to a backups directory before the first edit.
3. **Upgrade in place.** Never replace the dashboard with a disconnected standalone application. Custom modules are new routes/pages within the existing dashboard.
4. **Preserve everything.** Do not remove or alter existing routes, pages, navigation, APIs, auth, settings, jobs, tools, skills, memory, sessions, cron, or agent integration.
5. **Preserve Termux compatibility.** All changes must work on Android Termux with Python 3.14.6.
6. **Never expose credentials.** Never write API keys, tokens, passwords, or private config to files or chat. Use `hermes config set` for config changes.
7. **Do not touch other profiles.** Only modify the target profile (e.g. `everlily`). Never modify global settings or other Hermes profiles.
8. **Keep the application functional throughout.** Changes must be additive (new pages/themes), never destructive.
9. **Build and verify.** After frontend changes, run `npm run build` in the web directory and fix all TypeScript errors before declaring done.

## Procedure

### Phase 1 — Inspect (read-only)

1. Check Hermes version: `hermes --version`
2. Check active profile: `cat ~/.hermes/active_profile`
3. List profiles: `ls ~/.hermes/profiles/`
4. Verify target profile exists: `ls ~/.hermes/profiles/<name>/`
5. Find dashboard location: `~/.hermes` + Hermes install dir (`/data/data/com.termux/files/usr/lib/hermes-agent/app/`)
6. Identify frontend source: `app/web/src/` (React + TypeScript + Vite)
7. Identify backend: `app/hermes_cli/web_server.py` (FastAPI + uvicorn)
8. Identify web routers: `app/hermes_cli/web_routers/`
9. Identify built frontend: `app/hermes_cli/web_dist/`
10. Startup command: `hermes dashboard` (port 9119, host 127.0.0.1 by default)

### Phase 2 — Add Custom Theme

1. Add theme definition to `app/web/src/themes/presets.ts`:
   - Create a `DashboardTheme` object with name, label, description, palette, typography, layout, colorOverrides, seriesColors, swatchColors, terminalBackground, terminalForeground
   - Add it to `BUILTIN_THEMES` record
2. Register the same theme name in `app/hermes_cli/web_server_dashboard.py`:
   - Add an entry to `_BUILTIN_DASHBOARD_THEMES` list with matching `name`, `label`, `description`
   - The name string must match exactly between frontend and backend
3. Set the active theme via CLI: `hermes config set dashboard.theme <theme-name>`
   - Do NOT edit `config.yaml` directly — Hermes blocks direct writes to security-sensitive config

### Phase 3 — Add Custom Pages / Routes

1. Create page components in `app/web/src/pages/` (e.g. `StudioHomePage.tsx`, `StudioKanbanPage.tsx`)
2. Add lazy imports in `app/web/src/App.tsx`:
   - `const PageName = lazy(() => import("@/pages/PageName"));`
3. Add routes to `BUILTIN_ROUTES_CORE` record:
   - `"/studio/page": PageComponent,`
4. Add nav items to `BUILTIN_NAV_REST` array:
   - `{ path: "/studio/page", label: "Label", icon: Icon }`
5. Add icon to `ICON_MAP` if using a lucide-react icon not already mapped
   - Verify the icon exists in the installed lucide-react version (e.g. `Board` does NOT exist in v0.577 — use `List` instead)
6. Import UI components correctly:
   - `Button` from `@/components/ui/button` (NOT `@/components/ui/badge`)
   - `Card`, `CardContent`, `CardHeader`, `CardTitle` from `@/components/ui/card`
   - `Badge` from `@/components/ui/badge`
7. Remove all unused imports — TypeScript build fails on unused imports

### Phase 4 — Build

1. `cd /data/data/com.termux/files/usr/lib/hermes-agent/app/web`
2. `npm run build`
3. Verify Studio page assets appear in `app/hermes_cli/web_dist/assets/`
4. Fix any TypeScript errors iteratively before declaring success

### Phase 5 — Start and bind

1. Stop any existing dashboard: `hermes dashboard --stop`
2. Start with all-addresses binding: `hermes dashboard --port 9119 --host 0.0.0.0 --open-profile <profile>`
   - Use `--host 0.0.0.0` to make the dashboard reachable from any device on the network, not just localhost
   - The gateway runs via the default-profile multiplexer — check with `hermes gateway status`
3. Track the dashboard PID in `<profile_home>/runtime/dashboard.pid` for reliable stop/restart
   - `pgrep -f` can match its own shell command, causing false positives; scan `/proc/<pid>/cmdline` instead
4. Background processes receive SIGTERM when the parent shell/session ends — use persistent PID tracking

### Phase 6 — Document

1. Write startup, shutdown, recovery, and troubleshooting documentation
2. List all files modified and backed up
3. Record preservation statement (all existing functionality intact)

## Pitfalls

- **Direct config file edit is blocked.** Hermes refuses writes to `~/.hermes/profiles/<name>/config.yaml`. Use `hermes config set dashboard.theme <name>` instead. The agent tool will reject direct writes to security-sensitive configuration.
- **Unused imports fail the build.** TypeScript `noUnusedLocals` is enabled. Remove every unused import before running `npm run build`, or the build will error out.
- **`Board` icon missing in lucide-react v0.577.** The `Board` icon is not exported. Use `List` for kanban/task-board nav items instead. Always verify an icon exists in the installed version before importing.
- **`api.getProjects` does not exist.** The `@/lib/api` client does not expose every conceivable method. Check available methods before calling; do not call nonexistent API methods.
- **Theme name mismatch.** The `name` field in `presets.ts` must match exactly the `name` in `_BUILTIN_DASHBOARD_THEMES` in `web_server_dashboard.py`, and the value set via `hermes config set dashboard.theme`. Mismatch means the theme won't apply.
- **Icon import source.** Import lucide-react icons from `"lucide-react"`, not from UI component directories.
- **Bad imports cause silent runtime failures.** Importing `Button` from `@/components/ui/badge` instead of `@/components/ui/button` produces a TypeScript error at build time.
- **`pgrep` self-match false positive.** `pgrep -f "hermes_cli.main.*dashboard"` can match its own shell command line. Scan `/proc/<pid>/cmdline` instead, or track the dashboard PID in `<profile_home>/runtime/dashboard.pid`.
- **Background process SIGTERM.** A `hermes dashboard` process started from a shell receives SIGTERM when that shell/session exits. Use persistent PID tracking and `hermes dashboard --stop` for graceful shutdown.

## Related Skills

- `hermes-agent-engineer` — Hermes Agent architecture, profile management, memory configuration
- `termux-linux-engineer` — Android 14 and Termux support
- `creative-studio` — Announcements, project descriptions, creative content
- `cru-management` — Cookie Runners United club management

## Support Files

- `references/theme-palette.md` — Cookie Run Studio theme palette recipe (royal purple, blue, gold, White Lily accents)
