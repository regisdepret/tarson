# HEARTBEAT.md
> ⚠️ This file IS the procedure. Do not look for it elsewhere. Follow every line.

---

## HEARTBEAT — HARD RULES (Non-negotiable)

1. **OBSERVE AND REPORT ONLY** — never delete, archive, modify, or create tasks during a heartbeat
2. **QUIET HOURS: 21:00–08:00 EST** (Sundays: quiet until 09:00) — reply `HEARTBEAT_OK` unless CRITICAL
3. **No email subjects** — report count only; email details are Inbox Zero's job
4. **No repeating** — don't re-report something already flagged in the last 2 heartbeats
5. **Update state** — always write `memory/heartbeat-state.json` after each check

---

## STEP 1 — Check State
```bash
cat memory/heartbeat-state.json 2>/dev/null || echo '{"lastChecks":{"email":0,"tasks":0}}'
```
Skip a check if it ran <1h ago (email) or <2h ago (tasks).

---

## STEP 2 — Email Count (if >1h since last check)
```bash
bash scripts/inbox_fetch_all.sh
```
**Report format (if >0 emails):** `"📧 X unread emails"`
**DO NOT list subjects. DO NOT process. DO NOT delete.**

### Email Auto-Ignore Rules (apply silently — never report these)
- `FROM: no-reply@info.coinbase.com, SUBJECT: "Your recurring buy for"` → **IGNORE** (unless 4+ pile up)
- `FROM: hello@duolingo.com` → **IGNORE**
- `SUBJECT: "no events scheduled"` (Google Calendar) → **IGNORE**

---

## STEP 3 — Tasks (if >2h since last check)
```bash
GOG_KEYRING_PASSWORD=1234 gog tasks list dDQyYU42X00zUzVRQm0zQw --client tarson --account regis.depret@gmail.com --plain --max=100
```
**Report ONLY:**
- 🔴 **CRITICAL** — past due → notify immediately
- 🟠 **TODAY** — due today → notify once

**DO NOT report:** open/soon/watching tasks unless they just turned critical.

---

## STEP 4 — Update State
```bash
cat > memory/heartbeat-state.json << EOF
{"lastChecks":{"email":<epoch_now>,"tasks":<epoch_now>}}
EOF
```

---

## STEP 5 — Reply
- Nothing to report → `HEARTBEAT_OK`
- Something urgent → short alert message (no `HEARTBEAT_OK`)

---

---

## INBOX ZERO — HARD RULES (triggered by user, NOT heartbeat)

> Trigger: user says "go ahead", "process emails", "inbox zero", or similar.

### Before Starting — Read Rules
The full email rules live in `rules/inbox_zero.md`. Key rules embedded here:

| Sender / Subject | Action |
|---|---|
| `photos@onedrive.com` | Extract actual photo URLs, download, send to Telegram. Delete email + photo messages together. |
| `USPSInformeddelivery@...` | Extract mail images, analyze, show summary. Delete after review. |
| `no-reply@mailmais.com.br` | AUTO-DELETE, no presentation |
| `SUBJECT: "no events scheduled"` | SELF-DELETE silently, no user action |
| `no-reply@info.coinbase.com` + `"Your recurring buy for"` | IGNORE (don't present, don't delete) unless 4+ piling up |
| `noreply@kraken.com` + `"You bought BTC"` | AUTO-DELETE |
| `noreply@kraken.com` + `"You deposited funds"` | AUTO-DELETE |
| `SUBJECT: "feedback" OR "survey"` | AUTO-DELETE |
| `hello@duolingo.com` | AUTO-DELETE |
| `sc-noreply@google.com` | TRACK for website review |
| `admin@jmjimmigration.com` | NOTIFY URGENT |

### Flow (strict — no exceptions)
1. Fetch: `bash scripts/inbox_fetch_all.sh`
2. Apply auto-rules above silently (no buttons for auto-deletes)
3. Send intro message: *"You have X emails."* with **[Proceed] [Snooze]** buttons
4. Wait for Proceed
5. Present **ONE email** with summary + buttons. **STOP. WAIT.**
6. On callback:
   - Execute action
   - **DELETE the button message** (`message action=delete`) — ALWAYS, NO EXCEPTIONS
   - Present NEXT email
7. Repeat until done → send `"✅ Inbox zero."`

### Button Callback Formats
- `del_<threadId>` → `gog gmail thread modify <id> --add TRASH --client tarson --account regis.depret@gmail.com --force`
- `arc_<threadId>` → `gog gmail thread modify <id> --remove INBOX --client tarson --account regis.depret@gmail.com --force`
- `trk_<threadId>` → `bash scripts/track_email.sh track <id> <CATEGORY> "<title>"`
- `del2_<id1>_<id2>` → trash both threads
- Track = **Label_81 + INBOX stays** (never remove INBOX when tracking)

### Critical UX Rules
- **ONE email per message** — never batch multiple emails together
- **Delete button message on every callback** — dead buttons = broken UX
- **No acknowledgment messages** — no "Done ✅" replies after actions; just next email
- **Buttons must be inline** (use `buttons` param) — never write `/del_xxx` as text

---

## RESOLUTION WORKFLOW (when completing a task)
1. Add to task notes: `RESOLVED: YYYY-MM-DD — <what was done>`
2. Mark task complete: `gog tasks done <listId> <taskId> --client tarson --account regis.depret@gmail.com --force`
3. Clean up email: remove Label_81 + remove INBOX
4. If complex: create `memory/resolutions/YYYY-MM-DD-topic.md`
