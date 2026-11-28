---
name: selfhosted-git-publishing
description: "Use when publishing to a self-hosted git forge."
version: 1.0.0
author: EVERLASTING NEXUS
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [Git, Gitea, Self-hosted, Repository, Publishing]
    related_skills: [git-repo-publishing, hermes-agent-engineer, termux-linux-engineer]
---

# Self-Hosted Git Forge Publishing

Publishes a local folder as a new remote git repository on a self-hosted forge (Gitea, Gogs, etc.) using the forge's admin CLI and raw git over HTTP. Use `git-repo-publishing` instead when the target is GitHub (uses `gh`).

## Activation

Reference by name: `selfhosted-git-publishing`.

## When to use

- User asks to publish a local folder to a self-hosted Gitea instance.
- `gh` is not available or the forge is not GitHub.
- The forge is reachable over HTTP(S) and you have admin access to the forge CLI.

## Procedure

1. Confirm the forge server is running: `ps aux | grep -i gitea` (look for `gitea web --config ...`). Note the listen port (e.g. 3000).
2. Ensure the target user exists: `gitea admin user list` — if missing, create with `gitea admin user create --username <user> --password <pass> --email <email>`.
3. Set the account password for git-over-HTTP: `gitea admin user change-password --username <user> --password <pass> --must-change-password=false`. Git-over-HTTP authenticates with username+password, not an access token. The `--must-change-password=false` flag is required; without it the account is forced to reset on next login and git push will fail.
4. Generate an access token for REST API calls (if needed): `gitea admin user generate-access-token --username <user> --token-name <name> --raw --scopes all`. Tokens work for the REST API but NOT for git-over-HTTP push.
5. Initialize the local repo if needed: `git init && git branch -M master`.
6. Create a `.gitignore` excluding embedded sub-repos, caches, logs, and `.env`.
7. Stage and commit: `git add . && git commit -m "chore: publish to self-hosted forge

- description"`.
8. Add the remote and push: `git remote add origin "http://<user>:<pass>@<host>:<port>/<user>/<repo>.git" && git push -u origin <branch>`. Use `http://` (not `https://`) when the forge serves plain HTTP locally.
9. Verify: `git ls-remote http://<host>:<port>/<user>/<repo>.git` shows the pushed commit as HEAD.
10. Clean up scratch clones so no credentials remain on disk.

## Pitfalls

- Git-over-HTTP uses account password, not access token. Push with `http://<user>:<pass>@...`; the access token is only for REST API auth.
- Account flagged "must change password": always pass `--must-change-password=false` with `gitea admin user change-password`, or git push over HTTP will fail with "Update your password."
- Self-hosted Gitea may serve plain HTTP on a local port (e.g. 3000). Using `https://` will fail with a TLS "wrong version number" error.
- `gh` is not used for self-hosted forges — use `gitea` admin CLI + raw git instead.
- Access tokens generated with `gitea admin user generate-access-token` are single-use to retrieve; regenerate if lost (name must be unique).

## Example

```
User: "publish everlasting-bloom-os to gitea"
EVERLASTING NEXUS: Confirm gitea web is running, set captainabinav password with --must-change-password=false, commit everlasting-bloom-os files, push to http://captainabinav:pass@localhost:3000/captainabinav/everbloom-nexus.git master, verify with git ls-remote.
```