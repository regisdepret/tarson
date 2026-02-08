# Email Tracking Research - 2026-02-08

## Executive Summary

**Recommended Solution: Hybrid Label/Folder Approach**

Use Gmail labels + iCloud folders for tracking, monitored by TARSON in a unified way. This keeps emails in their native systems, supports inbox zero, and allows automation.

---

## Option Analysis

### Option 1: Email Folder/Label Approach

**Gmail Implementation:**
- Create label: `gog gmail labels create "Tracking" --account regis.depret@gmail.com`
- Move to tracking: `gog gmail batch modify <id> --add Tracking --remove INBOX`
- Return to inbox: `gog gmail batch modify <id> --add INBOX --remove Tracking`
- Delete when done: `gog gmail batch modify <id> --add TRASH`

**iCloud Implementation:**
- Create folder via IMAP: `CREATE "Tracking"` ✅ (tested - works)
- Move email: `COPY <id> "Tracking"` then `STORE <id> +FLAGS \Deleted` + `EXPUNGE`
- IMAP capabilities confirmed: UIDPLUS, SORT, CONDSTORE

**Pros:**
- ✅ Emails stay in native system (searchable, accessible)
- ✅ No data loss risk - original email preserved
- ✅ Simple mental model: "Tracking" folder = needs attention
- ✅ Works offline (email clients sync)
- ✅ Unified approach across both platforms

**Cons:**
- ⚠️ Two places to monitor (Gmail Tracking + iCloud Tracking)
- ⚠️ Need script to fetch both tracking folders
- ⚠️ No due dates or priority without additional metadata

**Implementation Complexity:** Low

---

### Option 2: Google Tasks Approach

**Implementation:**
```bash
# Create task linked to Gmail
gog tasks add @default --title "Follow up: Subject" \
  --notes "Email: https://mail.google.com/mail/u/0/#inbox/<threadId>\nFrom: sender@example.com\nID: <messageId>"
```

**Pros:**
- ✅ Single place to monitor all tasks
- ✅ Built-in due dates and organization
- ✅ Syncs to Google Calendar sidebar
- ✅ Can link back to original email via URL

**Cons:**
- ❌ Only works for Gmail (no iCloud email links)
- ⚠️ Email link is just text - if email is deleted, task becomes orphaned
- ⚠️ Requires copying email data to task notes for context
- ⚠️ Two-step process (move email + create task)
- ⚠️ Can't auto-detect when task is "resolved" from email state

**Implementation Complexity:** Medium (need email-to-task pipeline)

---

### Option 3: Hybrid - Labels + JSON Tracking File

**Concept:** Keep emails labeled/foldered, maintain a `tracking.json` with metadata:

```json
{
  "items": [
    {
      "source": "gmail",
      "id": "19abc123",
      "threadId": "19abc123", 
      "subject": "Contract Review",
      "from": "lawyer@firm.com",
      "added": "2026-02-08",
      "due": "2026-02-15",
      "notes": "Waiting for signature page",
      "link": "https://mail.google.com/..."
    },
    {
      "source": "icloud",
      "id": "42",
      "subject": "Appointment Confirmation",
      "from": "doctor@clinic.com",
      "added": "2026-02-08",
      "notes": "Need to confirm time"
    }
  ]
}
```

**Pros:**
- ✅ Single source of truth for TARSON
- ✅ Can add metadata (due dates, notes, priority)
- ✅ Emails stay in place, searchable
- ✅ Easy to automate cleanup
- ✅ Can detect orphaned entries (email deleted but tracking remains)

**Cons:**
- ⚠️ Requires sync discipline (add to tracking = add to folder)
- ⚠️ Another file to maintain

**Implementation Complexity:** Medium

---

## Blind Spots & Risks

| Risk | Mitigation |
|------|------------|
| Forget to check tracking folder | TARSON heartbeat includes tracking check |
| Email deleted but still tracked | Validate email exists before reporting |
| iCloud IMAP session limits | Use short connections, logout properly |
| Gmail label ID changes | Reference by name, not ID |
| Tracking folder gets cluttered | Weekly review to archive/delete resolved items |

---

## Recommended Implementation Plan

### Phase 1: Setup (One-time)

```bash
# Gmail: Create "Tracking" label
GOG_KEYRING_PASSWORD=1234 gog gmail labels create "Tracking" --account regis.depret@gmail.com

# iCloud: Create "Tracking" folder (via Python script)
# Already have IMAP capabilities confirmed
```

### Phase 2: Create Tracking Script

Create `scripts/email_tracking.py` with commands:
- `track <source> <id>` - Move email to Tracking, add to JSON
- `untrack <source> <id>` - Remove from Tracking, archive/delete
- `list` - Show all tracked emails with status
- `check` - Verify all tracked emails still exist

### Phase 3: TARSON Integration

Add to TARSON inbox flow:
1. Present unread emails with option: `[Track]` or `[Archive/Delete]`
2. Track = move to Tracking label/folder + add to tracking.json
3. During heartbeat: report tracked items approaching due date
4. Resolution: mark complete → auto-archive or delete source email

### Phase 4: Automation

When user resolves a tracked item:
```bash
# Remove from tracking
# Gmail: archive (remove INBOX if present)
gog gmail batch modify <id> --remove "Tracking,INBOX" --add "Processed"
# iCloud: move to Processed or delete
# Remove from tracking.json
```

---

## Command Reference (gog CLI)

```bash
# List Gmail labels
GOG_KEYRING_PASSWORD=1234 gog gmail labels list --account regis.depret@gmail.com

# Create label
GOG_KEYRING_PASSWORD=1234 gog gmail labels create "Tracking" --account regis.depret@gmail.com

# Add label to email (track it)
GOG_KEYRING_PASSWORD=1234 gog gmail batch modify <messageId> --add "Tracking" --remove "INBOX" --account regis.depret@gmail.com

# Remove from tracking (archive)
GOG_KEYRING_PASSWORD=1234 gog gmail batch modify <messageId> --remove "Tracking" --account regis.depret@gmail.com

# Get Gmail URL for email
GOG_KEYRING_PASSWORD=1234 gog gmail url <threadId> --account regis.depret@gmail.com
```

---

## Final Recommendation

**Go with Option 3 (Hybrid Label/Folder + JSON)**

Why:
1. **Best of both worlds** - emails stay native, metadata in one place
2. **TARSON-friendly** - single JSON to read for unified view
3. **Flexible** - can add due dates, notes, priorities
4. **Automatable** - can sync cleanup with email actions
5. **Recoverable** - if JSON lost, emails still in Tracking folders

Start simple with just the label/folder approach (Option 1), add JSON tracking as refinement.
