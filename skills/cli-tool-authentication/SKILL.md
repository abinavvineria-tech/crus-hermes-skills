---
name: cli-tool-authentication
description: "Use when authenticating CLI tools to Git/Gitea hosts."
version: 1.0.0
author: Everlasting Nexus
license: MIT
platforms: [linux, android, termux]
metadata:
  hermes:
    tags: [CLI, authentication, tea, Gitea, git, token, TTY]
    related_skills: [hermes-agent-engineer, automation-manager]
---

# CLI Tool Authentication (Git/Gitea Hosts)

## When to Use

Use this skill when you need to log a CLI tool (tea, gh, git, or similar) into a Git or Gitea host, especially in non-interactive terminals where the tool's interactive login flow requires a TTY that is not available.

## Always-on rules

- Never type, paste, or store user tokens in chat, skill files, or config. Have the user pass the token directly at runtime; the agent runs the command, the user supplies the secret.
- `tea login` (no subcommand) lists existing logins only — it does not prompt to create one. Use `tea login add` to create.
- Interactive `tea login add <url>` requires a TTY (`/dev/tty`). In non-interactive terminals (Termux CLI, CI, scripts) it fails with "could not open TTY."
- When interactive add fails due to TTY, use the non-interactive flag form: `tea login add --url <url> --name <name> --token <token>`.
- Equivalent env vars exist: `GITEA_SERVER_URL`, `GITEA_SERVER_TOKEN`, `GITEA_SERVER_USER`, `GITEA_SERVER_PASSWORD`.
- After login, register tea as a git credential helper so `git push`/`git clone` over HTTPS authenticate silently: `tea login helper setup` or pass `--git-credentials` at login time.
- Gitea tokens are displayed only once at generation. Copy immediately; there is no reveal later.

## Procedure

1. Check existing logins:
   ```
   tea login
   ```
2. Attempt interactive add:
   ```
   tea login add <url>
   ```
3. If TTY error, fall back to non-interactive:
   ```
   tea login add --url <url> --name <name> --token <token>
   ```
   (The user provides `<token>` directly; never log or echo it.)
4. Verify the login was added:
   ```
   tea login
   ```
5. Set up git credential helper:
   ```
   tea login helper setup
   ```
   Or include `--git-credentials` in step 3.

## Pitfalls

- Interactive TTY-dependent commands fail in non-interactive terminals — always check `--help` for a `--token` or equivalent non-interactive flag before concluding a tool is broken. The mechanism: the tool's init routine (e.g. clipboard discovery) calls `os.LookPath` which requires `/dev/tty`, and the Go runtime panics with SIGSYS when that device is absent.
- Do not hardcode tokens in scripts, aliases, or config files — tokens rotate and leaks persist in shell history.
- `tea login add --help` shows the full flag set; `--scopes` controls token permissions when creating a new token through tea.

## Example

User: "Log tea into my local Gitea at http://localhost:3000."
Agent: Run `tea login add --url http://localhost:3000 --name <name> --token <token>` with the token the user provides, then `tea login helper setup` for git push over HTTPS.