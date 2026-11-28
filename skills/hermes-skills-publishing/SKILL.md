---
name: hermes-skills-publishing
description: "Publish Hermes skills to GitHub registries and repos."
version: 1.0.0
platforms: [linux]
---

# Hermes Skills Publishing

Publishes Hermes skills (folders containing SKILL.md) to GitHub — either as individual public repos or as entries in a shared registry/collection repo via `hermes skills publish`.

## Activation

Reference by name: `hermes-skills-publishing`.

## When to use

- User asks to publish Hermes skills to GitHub.
- A folder of Hermes skills (each with SKILL.md) needs to be registered in a GitHub skill registry.
- Individual skill repos need to be created and pushed.

## Procedure

1. Verify each skill folder has SKILL.md at its root.
   - `ls <skill-folder>/SKILL.md` — if missing, do not publish; the skill is incomplete.

2. For individual public repos, use `gh`:
   - Confirm auth: `gh auth status` (must show `repo` scope).
   - Check repo doesn't exist: `gh repo view <org>/<skill-name>`.
   - Init git in skill folder: `git init -q && git branch -M main && git add . && git commit -q -m "chore: initialize <skill-name> skill repo"`.
   - Create `.gitignore` excluding `__pycache__/`, `*.pyc`, `.env`, `*.log`, `.DS_Store`.
   - Create repo: `gh repo create <skill-name> --description "<desc>" --public --source=. --remote=origin --push`.
   - Rate limit: GitHub anti-spam limits rapid repo creation — wait between calls if `createRepository` errors appear.

3. For registry publishing via Hermes:
   - `hermes skills publish --to github --repo <org>/<collection-repo> <skill-path>` — one skill per command.
   - Each call creates a PR in the collection repo.
   - Scan verdicts: BLOCKED means caution findings; PR is still created. Use `--force` to override if needed.
   - ALLOWED means safe; PR created normally.

4. Add documentation to collection repo:
   - Create `INSTALL.md` listing all skills, clone commands, and verification steps.
   - Commit and push to collection repo main.

## Pitfalls

- GitHub anti-spam rate limit on rapid `gh repo create` — space out calls or the createRepository GraphQL error appears. GitHub throttles repository creation velocity independently of the general API rate limit.
- `hermes skills publish` runs one skill at a time — loop over skill folders, do not batch multiple paths in one call.
- BLOCKED scan verdict does not stop PR creation — review findings before merging, or use `--force` to override.
- Skill folders that already contain `.git` will be added as embedded git repos/gitlinks — clone contents instead of nesting repos.
- `skills_list` may not show all skill directories — verify by listing the skills folder directly rather than trusting `skills_list` alone.

## Example

```
hermes skills publish --to github --repo abinavvineria-tech/crus-hermes-skills /path/to/skills/automation-manager
```