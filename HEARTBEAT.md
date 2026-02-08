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
- If resolved, mark task complete (keeps history)
