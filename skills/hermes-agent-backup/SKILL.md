---
name: hermes-agent-backup
description: "Use when backing up a Hermes Agent profile to GitHub."
version: 1.0.0
author: Everlasting Nexus
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [Hermes Agent, Backup, Profile, GitHub, Publish]
    related_skills: [hermes-agent-engineer, git-repo-publishing, termux-linux-engineer]
---

# Hermes Agent Backup

Back up a Hermes Agent profile with `hermes backup` and push the resulting archive to GitHub as its own public repository. Use this for the everlasting-nexus profile (Captain Abinav's CookieVerse / Cookie Runners United) and any other Hermes profile that needs a restorable off-profile copy.

## Activation

Reference by name: `hermes-agent-backup`.

## When to use

- User asks to back up a Hermes Agent profile and push the archive.
- A Hermes profile needs a restorable, versioned snapshot stored off the host.
- The active profile is uncertain — confirm before backing up (wrong profile = wrong data).

## Procedure

1. Confirm Hermes and the active profile.
   - `hermes --version` — note the installed version (v0.22.2 stable).
   - `cat ~/.hermes/active_profile` — confirm the profile name (e.g. `everlasting-nexus`).
   - `hermes profile describe <profile>` / `hermes profile show <profile>` — verify the profile path before writing anything.
   - Do not back up a different profile unless explicitly directed.

2. Run the backup.
   - Default full backup: `hermes backup`
     - Writes `~/hermes-backup-<timestamp>.zip` by default.
     - Backs up config, skills, sessions, and data; excludes the Hermes codebase, `backups/`, `cache/browser-use/`, `cache/scratch/`, `cache/uv/`, `installs/`, per-profile `backups/`, `cache/scratch/`, `cache/terminal-output/`, `lsp/node_modules/`, and every skill `.git/`.
   - Quick snapshot (critical state files only — config, state.db, .env, auth, cron): `hermes backup --quick -l <label>`.
   - Custom output: `hermes backup -o <path>`.
   - Record the emitted zip path, file count, original size, compressed size, and time from the command output.

3. Stage the backup artifact for publishing.
   - Create a work directory (e.g. under the profile's `cache/scratch/` or a dedicated folder).
   - Copy the zip into it: `cp ~/hermes-backup-<timestamp>.zip <workdir>/`.
   - Add a `README.md` documenting: source profile, Hermes version, zip path, file counts/sizes, and the restore command `hermes import <zip>`.
   - Add a `.gitignore` excluding `__pycache__/`, `*.pyc`, `.env`, `*.log`, caches, logs, and `backups/` — but do NOT exclude `*.zip`, because the backup zip is the deliverable to commit and push.
   - Initialize: `git init && git branch -M main`.
   - Stage: `git add .` (after `.gitignore` is in place).
   - Commit: `git commit -m "chore: initialize hermes-agent-backup repo

- Add Hermes Agent <profile> profile backup (hermes-backup-<timestamp>.zip)
  * <N> files, <original> MB original, <compressed> MB compressed
  * Restore with: hermes import hermes-backup-<timestamp>.zip"`.
   - Confirm `git status` is clean and branch is `main`.

4. Publish to GitHub (delegate publish mechanics to `git-repo-publishing`).
   - Confirm `gh auth status` shows `repo` scope.
   - Check whether a backup repo already exists for this profile (`gh repo list <owner>` or existing local remote) — do not create a duplicate.
   - Create and push: `gh repo create hermes-agent-backup --description "..." --public --source=. --remote=origin --push`.
   - If rate-limited with "too many repositories, too quickly," wait several minutes and retry the same `gh repo create`; do not re-run `git init` or `git commit`.

5. Verify.
   - `git remote -v` shows origin fetch/push.
   - `git branch -vv` shows `main` tracking `origin/main`.
   - `gh repo view <owner>/hermes-agent-backup --json name,description,visibility,url,createdAt` confirms the public repo exists.
   - Report the repo URL, commit, and restore command.

## Pitfalls

- `.gitignore` excluding `*.zip` publishes an empty repo — the backup zip is the deliverable, so never ignore `*.zip` in the backup repo.
- Backing up the wrong profile: always confirm `~/.hermes/active_profile` matches the intended profile before `hermes backup`; profile names are easy to confuse.
- `git add` of a folder that already contains a `.git` stages a gitlink (empty pointer) — stage only the staging workdir's own files, not nested repos.
- `gh repo create --push` fails if the working tree is dirty or commits are unpushed — ensure `git status` is clean before creating.
- `gh` not authenticated or missing `repo` scope: run `gh auth login` first.
- Full backup is ~105 MB original / ~21 MB compressed for a typical profile — expect the zip to be the largest staged file.

## Example

```
User: "make an backup for our new hermes agent and push it"
Everlasting Nexus: hermes backup -> ~/hermes-backup-2026-11-22-211535.zip (4457 files, 21.2 MB)
stage README.md + .gitignore (do not ignore *.zip) + zip in scratch dir
git init && git branch -M main && git add . && git commit
gh repo create hermes-agent-backup --public --source=. --remote=origin --push
verify remote, branch tracking, gh repo view
pushes to https://github.com/abinavvineria-tech/hermes-agent-backup
restore: hermes import hermes-backup-2026-11-22-211535.zip
```