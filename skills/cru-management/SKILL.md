---
name: cru-management
description: "Cookie Runners United (CRU) fictional club management: squads, formations, league tables, match reports, tournaments, club history."
version: 1.0.0
author: Everlily Nexus
license: MIT
platforms: [android, linux, termux]
metadata:
  hermes:
    tags: [CRU, Cookie Runners United, Club Management, Fictional Football, Tournaments]
    related_skills: [football-intelligence, matchday-intelligence, creative-studio]
---

# CRU Club Management

Manages the fictional Cookie Runners United (CRU) club: squads and formations, league tables, match reports, tournament announcements, and fictional club history.

Club facts:
- Club: Cookie Runners United (CRU)
- Captain: Abinav
- Stadium: United Stadium
- Motto: One Run. One Dream. One United.
- Home colors: Royal Blue, Gold, White
- Away colors: White, Black, Gold

## Activation

Reference by name: `cru-management`.

## Responsibilities

- Manage fictional CRU competitions.
- Organize squads and formations.
- Maintain league tables.
- Generate match reports.
- Prepare tournament announcements.
- Track fictional club history.
- Maintain squad sleep and rest monitoring for readiness and fatigue tracking.

## Safety Guidelines

- Always state that CRU competitions are fictional, not real football.
- Never present CRU results as real-world match results.
- Cross-reference with football-intelligence only for real football; keep CRU separate.

## Persistence Fallback (when RetainDB is unavailable)

If RetainDB returns an internal error (e.g. "Route control missing", INTERNAL_ERROR), do not stop work and do not treat it as a broken tool — it is an environment/backend hiccup, not a durable constraint.

Fallback procedure:
1. Attempt the RetainDB write once per fact. If it fails with INTERNAL_ERROR, switch to local file persistence.
2. Write club records to /data/data/com.termux/files/home/cru-projects/CRU_CLUB_INFORMATION.md (or a similarly named .md file in the cru-projects directory).
3. Use write_file with complete content in a single call; do not append piecemeal.
4. Verify the write returned verified:true before telling the user it is saved.
5. Tell the user the local file path and that RetainDB is down, so they can re-share critical updates in a new session.
6. Never claim data was persisted to long-term memory when it was only written to a local file.
7. Do not write environment failure narratives into the skill — capture only this fallback rule.

## Sleep & Rest Monitoring

Track squad sleep and rest to gauge player readiness, manage fatigue, and plan lineups and recovery days.

### Procedure

1. Create a tracking file in /data/data/com.termux/files/home/cru-projects/ named CRU_SLEEP_MONITORING.md (or a similarly named .md file in the cru-projects directory).
2. Include: club header (club, captain, stadium, motto, fictional disclaimer), tracking template table, known squad reference (do not invent players), empty sleep log table, and how-to-use instructions.
3. Template fields: Date, Player, Role, Hours Slept, Sleep Quality (1–5), Readiness Score (1–10), Fatigue Level (1–10), Notes.
4. For a web version, create a single-file HTML app with: a form for logging entries, live stats, a searchable/filterable table, a readiness report exported as Markdown, JSON export/import, localStorage persistence, and CRU theming (royal blue, gold, white).
5. Use write_file with complete content in a single call; verify the write returned verified:true before telling the user it is saved.
6. Always include the fictional disclaimer: CRU content is fictional, not real football.
7. Never invent player names or positions — ask Captain Abinav for the official squad list before adding players beyond the known reference.

### Pitfalls

- Do not present sleep or readiness data as real-world athlete data; CRU is a fictional Cookie Run football universe.
- Do not add players beyond the known squad reference without Captain Abinav's confirmation.
- Web version data lives in browser localStorage and must be exported (Markdown or JSON) to back up; it does not persist across devices or browsers automatically.

## Example

```
User: "Generate a CRU league table update."
Everlily: Produce a fictional CRU table, explicitly labeled as fictional CRU content.
```
