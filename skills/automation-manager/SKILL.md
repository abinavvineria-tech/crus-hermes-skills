---
name: automation-manager
description: "Scheduled tasks, recurring reports, reminders, authorized information-source monitoring, notifications, duplicate-job avoidance. Asia/Kolkata timezone."
version: 1.0.0
author: Everlily Nexus
license: MIT
platforms: [android, linux, termux]
metadata:
  hermes:
    tags: [Automation, Cron, Scheduled Tasks, Reminders, Reports, Notifications]
    related_skills: [matchday-intelligence, football-intelligence, hermes-agent-engineer]
---

# Automation Manager

Creates scheduled tasks, manages recurring reports, organizes reminders, monitors authorized information sources, prepares notifications, and avoids duplicate scheduled jobs. Uses the configured local timezone: Asia/Kolkata.

## Activation

Reference by name: `automation-manager`.

## Responsibilities

- Create scheduled tasks (cron).
- Manage recurring reports.
- Organize reminders.
- Monitor authorized information sources.
- Prepare notifications.
- Avoid duplicate scheduled jobs.

## Safety Guidelines

- Do not activate scheduled tasks without checking existing jobs and obtaining user authorization.
- List existing cron jobs before creating new ones (`cronjob(action='list')` / profile cron dir / `hermes cron list`).
- Avoid creating duplicate jobs for the same schedule.

## Creating a cron job via cronjob_manage

1. Load the schema: `tool_describe(names=['cronjob_manage'])` — `cronjob_manage` is a deferred tool, not directly callable.
2. Invoke: `tool_call(calls=[{name: 'cronjob_manage', arguments: {...}}])` with `action='create'`.
   - Required fields: `schedule` and `prompt`.
   - Keep the `prompt` single-line with no embedded double quotes — multi-line prompts with quotes break JSON serialization in `tool_call`.
   - `schedule` forms: `'every monday 9am'`, `'30m'`, `'every 2h'`, `'0 9 * * *'`, ISO timestamp `'2026-06-01T09:00:00'`.
   - Default `deliver='local'` saves output only — it is NOT delivered back into the chat. Use `deliver='telegram'` or `deliver='all'` for notification delivery (requires the Hermes gateway running).
3. Capture the `job_id` from the creation response — it is required for all subsequent management (`update`, `pause`, `resume`, `remove`, `run`). Never guess a job ID.
4. The gateway must be running for jobs to fire (`hermes gateway start`). If the gateway is down, jobs are saved but never execute.

## Planned schedule (Asia/Kolkata) — not yet activated

- Mon–Fri 5:00 PM: Upcoming football match report.
- Sat–Sun: Weekend matchday schedule, important match previews, confirmed results and summaries.

## Example

```
User: "Schedule the daily match report."
Everlily: Check existing jobs first, then ask for authorization before creating the cron job.
```

## Pitfalls

- `from hermes_tools import tool_call` fails inside the `execute_code` sandbox — only patch, read_file, search_files, terminal, web_extract, web_search, write_file are importable there. Use the normal `tool_call` tool instead.
- Repeated identical `tool_call` failures with the same arguments indicate a loop — inspect the error and change strategy rather than retrying unchanged.
- Jobs run in a fresh session with no current-chat context, so cron prompts must be fully self-contained; the agent cannot ask questions mid-run.
- `deliver='local'` is the default and means no chat delivery — the user will not see the output unless they check the job history manually.