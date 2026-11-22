# crus-hermes-skills

Hermes Agent skills collection for Cookie Runners United and programmers — part of Captain Abinav's CookieVerse.

🌸 EVERLASTING NEXUS — The Heart of CookieVerse  
Motto: Many stories, one universe, an everlasting bond.

## What's inside

This collection holds **16 custom Hermes skills** for the `everlasting-nexus` profile:

| # | Skill | Folder |
|---|---|---|
| 1 | automation-manager | `automation-manager/` |
| 2 | cookie-run-intelligence | `cookie-run-intelligence/` |
| 3 | creative-studio | `creative-studio/` |
| 4 | cru-management | `cru-management/` |
| 5 | football-intelligence | `football-intelligence/` |
| 6 | hermes-agent-engineer | `hermes-agent-engineer/` |
| 7 | hermes-dashboard-customization | `hermes-dashboard-customization/` |
| 8 | matchday-intelligence | `matchday-intelligence/` |
| 9 | omniroute-management | `omniroute-management/` |
| 10 | research-intelligence | `research-intelligence/` |
| 11 | telegram-bot-engineer | `telegram-bot-engineer/` |
| 12 | termux-linux-engineer | `termux-linux-engineer/` |
| 13 | cookie-run-fan-project | `cookie-run-fan-project/` |
| 14 | cookie-run-fan-web-app | `cookie-run-fan-web-app/` |
| 15 | flask-web-application | `flask-web-application/` |
| 16 | git-repo-publishing | `git-repo-publishing/` |

Each folder contains `SKILL.md` (the skill definition). Some also include a `references/` folder with supporting files.

## Install a skill into your Hermes profile

Clone the skill folder you want into your profile's skills directory:

```bash
# Replace <skill-name> with any folder name above
cd /data/data/com.termux/files/home/.hermes/profiles/everlasting-nexus/skills
git clone https://github.com/abinavvineria-tech/<skill-name>.git
```

Then verify it loaded:

```bash
skills_list
skill_view(name='<skill-name>')
```

## Install all skills at once

```bash
cd /data/data/com.termux/files/home/.hermes/profiles/everlasting-nexus/skills
for s in automation-manager cookie-run-intelligence creative-studio cru-management football-intelligence hermes-agent-engineer hermes-dashboard-customization matchday-intelligence omniroute-management research-intelligence telegram-bot-engineer termux-linux-engineer cookie-run-fan-project cookie-run-fan-web-app flask-web-application git-repo-publishing; do
  git clone https://github.com/abinavvineria-tech/$s.git
done
```

## Individual skill repos

Each skill is also published as its own public repo under `abinavvineria-tech`:

https://github.com/abinavvineria-tech/<skill-name>

Example: https://github.com/abinavvineria-tech/hermes-agent-engineer

## Using the git-repo-publishing skill

The `git-repo-publishing` skill documents how these repos were published:

```bash
skill_view(name='git-repo-publishing')
```

It covers `gh auth status`, `git init`, `.gitignore` for embedded sub-repos, `gh repo create --public --source=. --remote=origin --push`, and verification.

## Profile

- Active Hermes profile: `everlasting-nexus`
- Skills path: `/data/data/com.termux/files/home/.hermes/profiles/everlasting-nexus/skills/`
- GitHub account: `abinavvineria-tech`

---

🌸 EVERLASTING NEXUS — Many stories, one universe, an everlasting bond.
