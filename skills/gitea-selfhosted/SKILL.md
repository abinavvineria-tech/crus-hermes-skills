---
name: gitea-selfhosted
description: "Use when setting up Gitea on Termux without Docker."
version: 1.0.0
author: EVERLASTING NEXUS
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [gitea, self-hosted, git, termux, android]
---

# Gitea Self-Hosted on Termux (No Docker)

## When to Use
Use when setting up, configuring, or managing a self-hosted Gitea instance on Termux (Android) without Docker.

## Procedure

### 1. Verify Gitea installation
command -v gitea && gitea --version

Gitea binary is typically at /data/data/com.termux/files/usr/bin/gitea.

### 2. Locate configuration
- Config: /data/data/com.termux/files/usr/etc/gitea/app.ini
- Data dir: /data/data/com.termux/files/usr/var/lib/gitea/
- Log dir: /data/data/com.termux/files/usr/var/log/gitea/
- Database: SQLite3 at /data/data/com.termux/files/usr/var/lib/gitea/gitea.db

### 3. Edit app.ini (always read before patching)
Read the current app.ini with read_file, then apply targeted edits with patch.

Required [server] section keys:
[server]
DOMAIN = <ip-or-hostname>
HTTP_PORT = 3000
ROOT_URL = http://<ip-or-hostname>:3000/
DISABLE_SSH = false
START_SSH_SERVER = true
SSH_DOMAIN = <ip-or-hostname>
SSH_PORT = 2222
SSH_LISTEN_PORT = 2222

Required [security] section keys (generate via gitea generate secret SECRET_KEY and gitea generate secret INTERNAL_TOKEN):
[security]
INSTALL_LOCK = true
SECRET_KEY = <generated>
INTERNAL_TOKEN = <generated>

### 4. Initialize the database
gitea migrate --config /data/data/com.termux/files/usr/etc/gitea/app.ini

Run this before creating users. It sets up tables so gitea admin user create works.

### 5. Create admin user
gitea admin user create --username <name> --password <plaintext> --email <email> --admin --config /data/data/com.termux/files/usr/etc/gitea/app.ini

Always use plaintext password — Gitea hashes internally. Never pass a pre-hashed value.

### 6. Start the web server
gitea web --config /data/data/com.termux/files/usr/etc/gitea/app.ini &

Use terminal(action=background=true) so the process survives the call. Verify with curl -s http://<ip>:3000.

### 7. Reset password if login fails
gitea admin user change-password --username <name> --password <new-plaintext> --config /data/data/com.termux/files/usr/etc/gitea/app.ini

### 8. List users (verification)
gitea admin user list --config /data/data/com.termux/files/usr/etc/gitea/app.ini

## Pitfalls

- Do not patch app.ini without reading it first. The config may already contain [security] keys from a prior installation; appending duplicates breaks parsing.
- Run gitea migrate before gitea admin user create. Without migration, user creation fails silently because the user table does not exist.
- Do not pass a pre-hashed password. Gitea's admin user create expects plaintext and hashes it internally. Passing a SHA-1 hash results in double-hashing and login failure.
- Kill the old Gitea process before restarting. Two instances cannot bind port 3000 simultaneously; the second exits with bind: address already in use.
- SSH_DOMAIN must match the reachable host/IP. Clone URLs embed this domain; if users cannot reach it, SSH clones fail.
- INSTALL_LOCK = true in [security] prevents re-running the web installer. Only set after initial setup is complete.

## User preferences
- Non-Docker setup only (installed via pkg install gitea).
- Admin credentials saved to ~/gitea-admin-credentials.txt after creation.
- Access via LAN IP (e.g. 192.168.31.5:3000) as well as localhost.
- Keep tone friendly and concise; no markdown rendering in terminal output.