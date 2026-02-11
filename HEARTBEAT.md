# HEARTBEAT.md

## Tracking System Check

On each heartbeat, check TARSON-Tracking list for:

1. **Critical (🔴)** - Due today or past due → Notify immediately
2. **Soon (🟡)** - Due within 3 days → Notify once when entering window
3. **Watching (🟢)** - No due date → Include in weekly digest

### Check Command
```
GOG_KEYRING_PASSWORD=1234 gog tasks list dDQyYU42X00zUzVRQm0zQw --account regis.depret@gmail.com
```

### Notification Rules
- Critical items: Message Telegram immediately with urgency
- Soon items: Message Telegram once (track "last poke" in task notes)
- Watching items: Review weekly, poke if stale >7 days without update

### After Check
- Update "Last poke" in task notes
- If resolved, follow Resolution Workflow below

## Resolution Workflow

Before marking a task complete:

1. **Update task notes** with:
   ```
   RESOLVED: YYYY-MM-DD
   Resolution: Brief description of what was done
   ```

2. **Create resolution doc** (if detailed):
   `memory/resolutions/YYYY-MM-DD-topic.md`

3. **Reference doc in task notes**:
   `See: memory/resolutions/YYYY-MM-DD-topic.md`

4. **Then mark complete**

This keeps history searchable in both Google Tasks AND workspace files.
