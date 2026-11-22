---
name: flask-web-application
description: "Build Flask web apps with SQLite, admin CRUD, and tests."
version: 1.0.0
author: EVERLASTING NEXUS
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [Flask, SQLite, Web Application, Admin Dashboard, Testing, Documentation]
    related_skills: [termux-linux-engineer, hermes-agent-engineer]
---

# Flask Web Application Builder

Builds a complete, modern, responsive Flask web application with a SQLite database, password-protected admin CRUD, themed UI, search/filtering, empty/error states, JSON APIs, installation documentation, and test-client smoke verification.

## Activation

Reference by name: `flask-web-application`.

## When to Use

Use this skill when the task is to build a complete, server-rendered Flask web application with a SQLite database, password-protected admin CRUD, themed UI, search/filtering, empty/error states, JSON APIs, documentation, and git delivery. It covers scaffolding through smoke testing and commit/push. It is not for single-page React/Vite frontends (use termux-linux-engineer for those), nor for database-only or script-only tasks.

## Responsibilities

- Scaffold a Flask project (app factory, database module, routes, templates, static assets, config).
- Design and seed a SQLite schema; distinguish confirmed data from rumour/speculation.
- Implement public pages and password-protected admin routes.
- Add search, filtering, pagination-style empty states, and 404/500 handlers.
- Verify with Flask test-client smoke tests and a live-server curl check.
- Write README.md and installation.md; commit and push.

## Procedure

1. **Inspect conventions.** Read the repository's existing Flask app (app factory pattern, SQLite + session auth + flash messages, template layout) before writing new code. Match the project's style: Jinja2 macros, Werkzeug password hashing, `session`-based admin auth, `flash` messages.
2. **Scaffold the project.** Create `<project>/` with `app.py`, `database.py`, `requirements.txt`, `.env.example`, `.gitignore`, `static/css/style.css`, `static/js/app.js`, and `templates/`. Add a `tests/` directory.
3. **Define the database schema.** Create tables for the core entities (e.g., games, announcements, updates, events, calendar items, categories, users). Use `IF NOT EXISTS`, `PRAGMA foreign_keys = ON`, and `INSERT OR IGNORE` seeding so re-runs never overwrite existing rows.
4. **Seed confirmed data only.** Seed verified public-record entries; mark unverified items with `confirmation_status = 'rumour'` or `'speculation'` and add explicit `notes` warning they are not official news. Never present rumour as confirmed.
5. **Write query functions.** One function per entity for get/add/update/delete, returning `dict` rows. Keep SQL parameterized; never interpolate user input.
6. **Build routes.** Public routes first (dashboard, list, detail, tracker, upcoming, calendar, search, JSON APIs), then admin routes protected by a `login_required`-style decorator with session-based auth. Use `POST` for mutations and `GET` for reads.
7. **Create templates.** `base.html` with nav, flash message rendering, theme toggle hook, and footer disclaimer. Page templates extend `base.html` and import a shared `macros.html` for status/confirmation badges and date formatting.
8. **Style the UI.** Single CSS file with CSS custom properties for light/dark themes, cookie-inspired gradients, rounded cards, smooth transitions, mobile-first grid, and sticky header. Vanilla JS for theme persistence, mobile nav, flash auto-dismiss, and delete confirmations.
9. **Add error states.** 404 and 500 Jinja templates, empty-state components on list pages, loading-friendly structure, and form validation feedback.
10. **Write smoke tests.** Flask test-client suite covering: all public routes (200), missing entities (404), admin login protection (redirect), valid login (session set), add/edit/delete for every managed entity, seeded-data presence, and JSON API content-type. Run with a temp `CRU_DATABASE_PATH`.
11. **Verify live.** Start `python app.py` with `debug=False` and `curl` key routes to confirm the server binds and returns 200.
12. **Document.** `README.md` with features, tech stack, quick start, env vars, project structure, confirmation-level policy, and testing. `installation.md` with prerequisites, venv + dependency install, env configuration, run instructions, admin first login, database reset, and production notes.
13. **Commit and push.** Stage only project paths, commit with a focused message, push to the default branch. Do not commit `.env` or the `.db` file.

## Pitfalls

Pitfall: Run the Flask dev server with `debug=False` on Termux/Android Python 3.14. The Werkzeug debug reloader imports `multiprocessing`, which fails on this toolchain with `_PyType_AllocNoTrack` symbol errors in `array.cpython-314-aarch64-linux-android.so`. The app itself works fine; only the reloader crashes.

Pitfall: `uv venv .venv` creates a venv without `pip`. Install dependencies with `uv pip install --python .venv -r requirements.txt`, not `.venv/bin/pip install`.

Pitfall: `/tmp` may be read-only on Android/Termux. Write temporary files to the Hermes scratch directory (`/data/data/com.termux/files/home/.hermes/profiles/<profile>/cache/scratch`) instead.

Pitfall: GitHub API calls for issue comments or closing issues return `401 Requires authentication` without a token. Do not attempt to close issues or post comments via the API unless a `GITHUB_TOKEN` is available; document the summary for manual completion instead.

Pitfall: Do not hard-code the active navigation item. Derive it from `request.path` so direct links and browser back/forward remain correct.

Pitfall: Never expose an unimplemented feature as a clickable control. Mark planned features as planned or give them a real destination; decorative affordances create false functionality.

Pitfall: Do not claim all controls are functional from a successful build alone. Verify with live-server `curl` checks and test-client coverage of each route.

Pitfall: Never store API keys, secrets, or credentials in the repository. Use `.env.example` (gitignored `.env`) and read secrets via `os.environ.get` with safe defaults.

Pitfall: Do not interpolate variables into SQL strings. Use parameterized queries everywhere; `INSERT OR IGNORE` for idempotent seeding.

## User Preferences

- Match existing repo conventions: Flask app factory, SQLite database, Werkzeug password hashing, Flask `session` admin auth, `flash` messages, Jinja2 templates with macros.
- Premium Cookie Run-inspired fantasy interface: magical-kingdom atmosphere, cookie-themed gradients, rounded cards, smooth animations, mobile-first responsive design, dark and light themes.
- Distinguish confirmed announcements from rumours and speculation; never present rumours as official news.
- Do not copy official artwork or pretend the product is official.
- Be direct: match reply length to the weight of the ask, no filler, plain claims over adjectives.

## Support Files

- `references/termux-flask-workarounds.md` — Termux/Android-specific workarounds for running Flask (debug reloader, uv venv pip, /tmp read-only).

## Example

```
User: "Build a Flask information hub with admin CRUD and tests."
Everlily: Scaffold app.py + database.py + templates + static, write tests/smoke_test.py, run it, write README.md and installation.md, commit and push.
```
