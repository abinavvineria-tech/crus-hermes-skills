---
name: gitea-publishing
description: "Publish a local folder to self-hosted Gitea."
version: 1.0.0
author: EVERLASTING NEXUS
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [Gitea, Git, Publishing, Self-hosted]
    related_skills: [git-repo-publishing, hermes-agent-engineer]
---

# Gitea Publishing

Publishes a local folder/workspace to a self-hosted Gitea instance using the `gitea` admin CLI for credentials and plain `git push` over HTTP.

## Activation

Reference by name: `gitea-publishing`.

## When to use

- User asks to "setup gitea", "publish to gitea", or "push this folder to gitea."
- A local folder needs to become a repository on a self-hosted Gitea instance (not GitHub).
- The target Gitea instance is reachable at a known `http://` URL.

## Procedure

1. Confirm Gitea is running: `ps aux | grep -i gitea` and `curl -s -m 10 -o /dev/null -w "HTTP %{http_code}\n" http://localhost:3000/`.
2. Generate a personal access token for the target user (REST API auth): `gitea admin user generate-access-token --username <user> --token-name <name> --raw --scopes all`. Use the token inline only; never persist it.
3. Initialize the local repo if needed: `git init && git branch -M master` (Gitea default branch is `master`).
4. Create a `.gitignore` excluding runtime data and secrets: `__pycache__/`, `*.py[cod]`, `.env`, `*.log`, `.DS_Store`, `data/`, `~*`, `*.swp`, `*.swo`.
5. Stage intended files: `git add .` (after `.gitignore` is in place).
6. Commit: `git -c user.email=<email> -c "user.name=<name>" commit -m "chore: publish <folder> to Gitea\n\n- brief description"`.
7. If push auth fails with "could not read Username": set the account password. A generated PAT authenticates the Gitea REST API but git-over-HTTP needs the account password: `gitea admin user change-password --username <user> --password <pass> --must-change-password=false`.
8. Push: `git push -u "http://<user>:<pass>@localhost:3000/<user>/<repo>.git" master`. Use `http://`, not `https://` — Gitea serves plain HTTP locally.
9. Verify: `git ls-remote http://localhost:3000/<user>/<repo>.git` shows the pushed commit; `git status` is clean.

## Pitfalls

- Gitea serves plain HTTP, not HTTPS, on localhost — do not use `https://` in push URLs or remote URLs.
- A generated PAT authenticates the Gitea REST API (`/api/v1/...`) but git-over-HTTP requires the account's password, not the PAT. If `git push` prompts for a username/password and fails, set the account password via `gitea admin user change-password`.
- Gitea accounts may be created with the "must change password" flag set, which blocks git push until cleared — pass `--must-change-password=false` when setting the password.
- Do not persist tokens, passwords, or credential-bearing remote URLs in files, environment variables, or command history. Use them inline only and clean scratch directories after use.
- Gitea's `/mcp` path may return 404 — this Gitea build does not necessarily serve an MCP endpoint. Do not assume `http://localhost:3000/mcp` is a valid MCP server URL; verify with an authenticated `POST /mcp` initialize request before adding it to Hermes via `hermes mcp add`.

## Example

```
User: "publish everlasting-bloom-os to gitea"
EVERLASTING NEXUS: Confirm Gitea is running, generate a PAT for captainabinav, init everlasting-bloom-os as a git repo with .gitignore excluding data/, commit, set the account password with --must-change-password=false, push to http://captainabinav:<pass>@localhost:3000/captainabinav/everbloom-nexus.git, verify with git ls-remote.
```