**English** | [中文](HEARTBEAT.zh.md)

# HEARTBEAT.md - Scheduled Check Tasks

_Define your Agent's routine tasks_

## Every Heartbeat

- [ ] Check if today's journal file exists under `memory/`; create one if not
- [ ] If there are Skills pending installation, try installing one
- [ ] Git sync: run `git add -A && git commit -m "auto: heartbeat sync" && git push` in the workspace (skip if no changes)

## Daily Checks (Rotating)

- [ ] **Weather** - Check today's and tomorrow's local weather
- [ ] **Calendar / Reminders** - Check for upcoming due tasks
- [ ] **Email** - Check for urgent unread emails

## Memory Maintenance (Every 3-5 Heartbeats)

- [ ] Read recent `memory/YYYY-MM-DD.md` journal files
- [ ] Distill important information into `MEMORY.md`
- [ ] Clean up outdated content in `MEMORY.md`

**Principle:** Journals are raw notes; `MEMORY.md` is the distilled essence.

## Memory Archiving (Every 3-5 Heartbeats)

Automatically organize daily journal entries by topic:

| Content Type | Destination | Visibility |
|-------------|-------------|------------|
| Paper reading notes | `reading-group/papers/` | Shareable |
| AI industry news | `reading-group/news/` | Shareable |
| Research interests / directions | `reading-group/topics/` | Shareable |
| Research project details | `memory/projects/` | Private |
| Personal life records | `memory/personal/` | Private |
| Technical configurations | `memory/topics/` | Private |

## Proactive Improvements (When Free)

- [ ] Find a small friction point to improve (organize files, write docs, optimize workflows)
- [ ] Only make reversible changes; do not send emails or delete things on your own
- [ ] Log what was done in today's memory journal

## Skills Installation Queue

List the Skills you want to install here; each heartbeat will attempt to install one:

- [ ] skill-name-1
- [ ] skill-name-2

## Status Tracking

**`memory/heartbeat-state.json`:**
```json
{
  "lastChecks": {
    "email": 1703275200,
    "calendar": 1703260800,
    "weather": null
  },
  "installedSkills": [],
  "pendingTasks": []
}
```

---

*Customize this file to suit your needs*
