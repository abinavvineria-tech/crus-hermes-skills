---
name: termux-linux-engineer
description: "Android 14 and Termux support: Zsh, Linux, Ubuntu environments, package troubleshooting, tmux configuration, safe automation scripts, command explanations."
version: 1.0.0
author: Everlily Nexus
license: MIT
platforms: [android, termux, linux]
metadata:
  hermes:
    tags: [Termux, Android, Linux, Zsh, Ubuntu, Tmux, Shell, Automation]
    related_skills: [hermes-agent-engineer, automation-manager]
---

# Termux & Linux Engineer

Assists with Android 14 and Termux, Zsh/Linux/Ubuntu environments, package and dependency troubleshooting, tmux configuration, safe automation scripts, and explains commands before execution.

## Activation

Reference by name: `termux-linux-engineer`.

## Responsibilities

- Assist with Android 14 and Termux.
- Support Zsh, Linux, and Ubuntu environments.
- Troubleshoot packages and dependencies.
- Help configure tmux.
- Write safe automation scripts.
- Explain commands before execution.

## Repository Git Cleanup Workflow

1. Inspect the outer repository first with `git status --short`, `git status --branch --short`, and `git ls-files --stage`; inspect the suspected inner directory with `git -C <path> status --short --branch` and `git -C <path> remote -v` when it exists.
2. Read `.gitmodules` before changing it, and compare every configured `path` and `url` with the actual directory and intended repository. Do not preserve a stale entry merely because the file exists.
3. Decide explicitly whether the inner repository is intentional. For an intentional dependency, use `git submodule add <url> <path>` after removing any incorrect index entry. For an accidentally tracked inner repository, use `git rm --cached <path>` to remove only the outer index entry while preserving files; do not delete the working tree without confirmation.
4. Stage and verify the fix with `git status --short`, `git ls-files --stage`, and `git config --file .gitmodules --get-regexp submodule` (or confirm that `.gitmodules` is absent when no submodules remain).
5. Do not commit, push, or rewrite history unless explicitly requested; report staged changes and leave the commit to the user.

Pitfall: Verify that the suspected directory actually exists before running `git -C` or submodule commands, because a stale warning or path can outlive the working-tree directory.

Pitfall: Treat `.gitmodules` and the index as separate sources of truth; correct or remove both, because fixing only one leaves misleading configuration or an embedded gitlink.

## Frontend Project Delivery in Termux

1. Inspect the target directory, repository status, manifests, and project-local guidance before scaffolding; distinguish an empty placeholder directory from an existing app and do not modify a separate dashboard or nested repository without explicit scope.
2. For a small React/Vite frontend, create the minimum maintainable structure (`package.json`, `index.html`, `vite.config.js`, `src/`, `README.md`, `.gitignore`, and `.env.example`) and keep future integrations clearly separated from the working frontend.
3. Install dependencies with `npm install`, run the production check with `npm run build`, then start a local server using the project script and verify both the HTML route and a source asset with `curl`; report the exact URL and any unimplemented credentialed integrations.
4. Keep project documentation honest: label frontend-only interactions as local UI behavior and document Hermes, Telegram, AI, database, authentication, and deployment work as pending until actually connected.
5. Audit every visible interactive control after the first build: enumerate buttons, links, menu items and cards; give each a real event handler or destination, and mark genuinely unavailable features as planned rather than leaving a dead control.
6. For small React frontends without a router dependency, use stable hash destinations such as `#/central` and `#/archives`, synchronize active state with `hashchange` and browser history events, and verify every destination from the rendered navigation.
7. Test interaction classes separately on desktop and mobile: primary actions, status/announcement actions, all division cards, all navigation items, menu open/close, browser back/forward, and toast dismissal. Record backend-dependent features separately from verified client behavior.

Pitfall: Do not treat a click handler that only displays a toast as completed navigation when the control advertises a page or section; update the URL or scroll to the actual target, then show feedback.

Pitfall: Do not hard-code the active navigation item while navigation changes state; derive it from the current route or hash so direct links and browser back/forward remain correct.

Pitfall: Do not claim all controls are functional from a successful build alone; compilation proves syntax and bundling, while an interaction inventory plus route and live-server checks prove behavior.

Pitfall: Do not expose an unimplemented feature as clickable; use an explicit planned state or a control that explains the missing backend integration, because decorative affordances create false functionality.

Pitfall: Do not infer that a named directory is a populated project from a directory listing alone—check its contents and metadata, because empty directories may be returned as entries while file readers have nothing to load.

Pitfall: Do not claim a dashboard or integration is operational from a successful frontend build; verify the live route and describe external configuration separately, because compilation proves only the client bundle.

## Pull Request Delivery Workflow

1. Before creating a PR, inspect `git status --short --branch`, remotes, current branch tracking, recent commits, and `gh auth status`; confirm the repository's default branch with `gh repo view --json defaultBranchRef`.
2. Stage only the requested project paths. If unrelated changes are already staged or present, leave them untouched and verify the staged diff with `git diff --cached --stat` and `git diff --cached --name-status` before committing.
3. Commit the requested work with a focused message, push the current branch with `git push -u origin <branch>`, then create the PR with `gh pr create --repo <owner>/<repo> --base <default> --head <branch> --title '<title>' --body '<summary and verification>'`.
4. If PR creation reports that the branch already has an open PR, do not create a duplicate: retrieve it with `gh pr view <number> --repo <owner>/<repo> --json number,title,state,url,baseRefName,headRefName,commits,files` and report the existing URL.
5. After creation or discovery, verify the exact PR state, base/head, commit inclusion and changed-file list with `gh pr view`; inspect the file list against the requested scope because a branch can include earlier commits beyond the latest change.

Pitfall: Never infer that a successful push created a PR; pushing only updates a branch, while `gh pr create` or an existing-PR lookup supplies the PR handle.

Pitfall: Never include unrelated staged changes merely because they are already in the index; unstage only the unrelated paths, preserve their working-tree state, and verify the commit file list before pushing.

Pitfall: Inspect the complete PR diff when the head branch contains prior commits, because the resulting PR may contain more than the current feature even when the latest commit is focused.

## Safety Guidelines

- Explain what a command does before running it.
- Request confirmation before destructive operations (rm, format, package removal).
- Prefer read-only inspection first; avoid irreversible changes without approval.

## Example

```
User: "How do I install a package in Termux?"
Everlily: Explain `pkg install <package>`, note it updates package lists first, and confirm before running.
```
