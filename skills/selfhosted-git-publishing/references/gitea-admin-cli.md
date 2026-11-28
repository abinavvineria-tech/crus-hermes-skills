# Gitea Admin CLI Reference

Common `gitea admin` subcommands for self-hosted forge publishing:

- `gitea admin user list` — list all users
- `gitea admin user create --username <u> --password <p> --email <e>` — create a user
- `gitea admin user change-password --username <u> --password <p> --must-change-password=false` — set password without forcing reset
- `gitea admin user generate-access-token --username <u> --token-name <n> --raw --scopes all` — generate REST API token
- `gitea admin user delete --username <u>` — delete a user

Notes:
- Git-over-HTTP uses username+password authentication.
- REST API uses bearer token in the `Authorization: token <token>` header.
- Token names must be unique per user.

Config locations (Termux install):
- Config: /data/data/com.termux/files/usr/etc/gitea/app.ini
- Work path: /data/data/com.termux/files/usr/var/lib/gitea
- Git data: /data/data/com.termux/files/usr/var/lib/gitea/git-data
- Database: /data/data/com.termux/files/usr/var/lib/gitea/gitea.db (sqlite3)
