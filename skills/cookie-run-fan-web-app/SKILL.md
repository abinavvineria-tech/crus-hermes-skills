---
name: cookie-run-fan-web-app
description: "Build Cookie Run fan web apps with Flask and SQLite."
version: 1.0.0
author: Everlily Nexus
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [Cookie Run, fan-project, Flask, SQLite, web-app, admin-CRUD]
    related_skills: [cookie-run-intelligence, creative-studio]
---

# Cookie Run Fan Web Application

Build unofficial, fan-made Cookie Run-inspired web applications using Flask + SQLite + Jinja2. Covers project scaffolding, database schema and seeding, public and admin routes, mobile-first CSS, starfield particles, and test-client verification.

## When to Use

Use when creating or extending a Cookie Run fan website/dashboard (Flask backend, SQLite storage, Jinja2 templates, mobile-first responsive UI, admin-protected content management).

## Always-on rules

- Every page must carry an unofficial fan-project disclaimer banner stating the project is not affiliated with or endorsed by Devsisters Corporation or the official Cookie Run games.
- Use the CRU/Everlasting color palette: royal blue (#1e3a8a), gold (#d4af37), white, magical purple (#7c3aed).
- Mobile-first responsive design; navigation collapses to a hamburger menu on small screens.
- No placeholder buttons that do nothing; every interactive element must be wired to a working route or action.
- Admin functionality is password-protected with session-based authentication; destructive actions require client-side confirmation via data-confirm.
- Persistent storage via SQLite; database is initialized and seed-friendly content inserted only when tables are empty.
- Use Werkzeug password hashing for admin accounts, never plaintext passwords.
- Use original CSS-generated visuals only; no copyrighted Cookie Run artwork or assets.

## Procedure

1. Scaffold the project directory:
   - app.py — Flask application and all routes
   - database.py — SQLite connection, init_db, seed data
   - schema.sql — table definitions with CHECK constraints
   - requirements.txt — Flask>=3.0,<4.0 and Werkzeug>=3.0,<4.0
   - static/css/base.css, components.css, particles.css
   - static/js/app.js, particles.js
   - templates/base.html plus page templates, admin/ subfolder, errors/ subfolder (project files, not skill files)

2. Write schema.sql first. Define tables for settings, characters, friendships, worlds, stories, adventures, timeline_events, community_members, future_projects, admin_users. Every CHECK constraint must list every status/category value the seed data will use.

3. Write database.py:
   - get_db() opens a per-request sqlite3 connection with row_factory=sqlite3.Row
   - close_db() closes it at teardown_appcontext
   - init_db() executes schema.sql, then inserts default settings and seeds tables only when empty (SELECT COUNT(*) = 0)
   - Hash the default admin password with werkzeug.security.generate_password_hash

4. Write app.py:
   - Flask app with template_folder and static_folder
   - SECRET_KEY from env with a dev fallback
   - context processor injecting all settings into every template
   - login_required decorator redirecting to admin_login with next= when session['admin'] is not set
   - Public routes: home, cookieverse, characters, character_detail, timeline, adventures, adventure_detail, community, future, about
   - Admin routes: login (GET/POST), logout, dashboard, characters CRUD, timeline CRUD, settings — all wrapped in @login_required
   - Error handlers for 404 and 500 rendering errors/404.html and errors/500.html
   - __main__ block calls init_db() then app.run()

5. Write templates/base.html:
   - Unofficial fan-project banner at the top
   - Sticky nav with logo, links, mobile hamburger toggle
   - Flash message rendering with categories
   - main > content block > footer
   - Canvas #starfield for particles, scripts loaded with defer

6. Write CSS in three files:
   - base.css: design tokens (--color-primary, --color-accent, --color-magic, etc.), reset, typography, layout, components, media queries, prefers-reduced-motion
   - components.css: feature grid, hero, character/world/adventure cards, timeline, forms, admin table, stat cards, progress bars
   - particles.css: #starfield canvas fixed full-screen, z-index below content

7. Write JavaScript:
   - particles.js: canvas starfield, low particle count for mobile, wraps particles, pulses alpha, pauses on visibility hidden, respects prefers-reduced-motion
   - app.js: mobile nav toggle, dismissible flash messages, active nav highlighting, data-confirm on destructive forms

8. Write page templates extending base.html with {% block content %}. Use Jinja conditionals for empty states. Pass data from routes as Row objects.

9. Write admin templates: login form, dashboard with stat cards, list tables with edit/delete links, create/edit forms with checkbox_group and settings_grid layouts. Delete forms use method="post" with data-confirm.

10. Verify with Flask test client (not a long-running server):
    - All public routes return 200; missing detail IDs return 404
    - Admin routes without login return 302
    - Login works with correct password, rejected with wrong
    - Admin CRUD: create, edit, delete all persist to the database
    - Settings updates persist

## Pitfalls

- Do not use `--` SQL-style comments inside Python code; they are SyntaxErrors. Use `#`.
- Do not write `rgba(#hex)` in CSS inline styles; hex color strings are not valid inside rgba(). Use `background-color: #hex` directly.
- Schema CHECK constraints must include every status/category value used in seed data, or inserts will fail with IntegrityError.
- Jinja `{% endif %}` (and similar tags) must not have a space before the closing `}`; it raises TemplateSyntaxError unexpected '}'.
- Background terminal servers are killed with SIGTERM when the shell session ends; do not rely on a long-running `python app.py` for verification. Use app.test_client() instead.
- /tmp is read-only in this environment; write probe/test scripts to the Hermes profile scratch directory.
- When importing app from a test script outside the project folder, insert the project path into sys.path before import.
- get_setting and app context helpers live in app.py, not database.py.