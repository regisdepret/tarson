# 2026-02-13 Afternoon Session

## Tracking System Review & Cleanup

### Tasks Completed Today
1. ✅ Google Local Services - New lead call (resolved, deleted email)
2. ✅ Prudential Brasil - Policy cancellation review (resolved, deleted emails from tracking)
3. ✅ Fragment: Blue Ridge visit scheduling (user already completed)
4. ✅ Jobber Summit event (completed)
5. ✅ Merged duplicate Jobber onboarding tasks → single "[ACTION] Jobber - Complete onboarding"

### Tasks Updated
1. **JMJ EB3 Immigration**
   - Changed from [ACTION] to [WAITING] - company info was already sent
   - Sent follow-up email requesting confirmation of 2023 tax docs receipt
   - Email sent to admin@jmjimmigration.com (thread: gmail:19c587c799776a39)

2. **MyOls SO180213 Monitoring**
   - Changed from [WAITING] to [WATCHING]
   - Added 60-day monitoring period (until April 10, 2026)
   - Purpose: Verify sales order deletion

3. **Bon Jovi Concert**
   - Added full details to task notes:
     - Madison Square Garden, NYC
     - Thursday, July 16, 2026 at 7:30 PM
     - Section 226, Row 17, Seats 9-10
     - Need to plan NYC trip from Atlanta

### Tasks Cleaned Up
Deleted 19 completed tasks from TARSON-Tracking list to clean slate

### New Tracking Items Created
1. **[TAX] T&F Associates - 2025 Tax Preparation** (Due: Feb 27)
   - Fee: $605.00
   - Documents submitted Feb 13
   - Source: gmail:19c53493042c7ce6

2. **[WORK] Jobber - Verify workflow created for Invoice #7001**
   - Payment: $4,250.00
   - Customer: Revive Homes and Renovations Limited
   - Source: icloud:uid:4764
   - Action: Check if workflow auto-created after payment

### Active Tasks Summary (18 total)
**🔴 Due Soon:**
- National Life Group auto-draft (Feb 15)
- T&F Associates taxes (Feb 27)

**📅 Scheduled:**
- Archadeck Hardscape (Feb 23)
- ontidwit AI Agents session (Feb 25)
- Chestnut job GLM quote (Feb 28)
- MyOls SO180213 monitoring (Apr 10)
- Bon Jovi NYC (Jul 16)

**⏳ Actions:**
- Jobber - Complete onboarding
- Jobber - Schedule training session
- Jobber - Verify workflow for Invoice #7001
- Configure Google Review iOS Shortcut
- A Plus website - Add Quick Quote form

**⏱️ Waiting:**
- JMJ tax info confirmation (email sent today)
- JMJ EB3 company info confirmation (email sent today)

**👀 Watching:**
- Oracle ARM Instance cron (566+ attempts, still no capacity)
- MyOls SO180213 deletion (60-day monitor)
- Google Tasks notification integration

## Inbox Zero Workflow Improvements

### Jobber Payment Confirmations
**New rule:** Always include "Track Workflow Check" button for Jobber payment confirmations
- Purpose: Verify that workflow was auto-created in Jobber after payment received
- Creates task in TARSON-Tracking for follow-up

### OneDrive Memories Extraction - Working Method Confirmed
**Verified extraction process:**
1. Get email JSON: `gog gmail thread get <id> --json`
2. Extract HTML: `jq -r '.thread.messages[0].payload.parts[] | select(.mimeType == "text/html") | .body.data'`
3. Decode base64: `tr '_-' '/+' | base64 -d`
4. Find photo URLs: `grep -oP 'https://my\.microsoftpersonalcontent\.com/[^"<>\s]+'`
5. Download with User-Agent: `curl -sL "$url" -H "User-Agent: Mozilla/5.0" -o file.jpg`
6. Send via message tool with media parameter

**Key learnings:**
- Must decode HTML body first
- Tempauth tokens valid for ~30 days
- Always show images FIRST, then present summary with buttons
- On delete: trash email, delete button message, rm local files

### Task Review UI Enhancement
**Interactive task navigation implemented:**
- One task at a time with inline buttons
- Actions: Complete, Delete, Next, Previous
- Context-specific actions (Schedule, Follow Up, etc.)
- Message deletion between tasks keeps chat clean

**User feedback:** Bulk presentation is useless - one-by-one is the right approach

### Email Threading Cleanup
**Fixed persistent inbox labels:**
- Issue: Some emails showed in inbox with TRASH or Tracking labels
- Solution: Use `thread modify --remove INBOX` instead of `batch modify`
- `batch modify` only affects one message in thread
- `thread modify` affects ALL messages in thread

## Infrastructure Notes

### Oracle ARM Instance Status
- Cron running every 5 minutes since Feb 11
- 566+ failed attempts
- Error: "Out of host capacity"
- Region: US-ASHBURN (all 3 availability domains)
- Status: Working correctly, just waiting for Oracle capacity

### Google Calendar Integration
**Limitation discovered:** `gog` CLI doesn't support creating calendar events
- Can only list calendars and get events
- No `create event` command available
- Workaround: Update task notes with full details for manual calendar entry

## Communication

### Email Sent Today
**To:** JMJ Immigration Services (admin@jmjimmigration.com)
**Subject:** Re: Tax information - Confirmation
**Content:** Follow-up requesting:
- Confirmation of 2023 tax docs receipt (sent by accountant yesterday)
- Any additional documentation needed for EB3 application
**Thread ID:** gmail:19c587c799776a39

## Lessons Learned

1. **Task UI Design:** Interactive one-at-a-time navigation >> bulk lists
2. **Context Actions:** Buttons should offer relevant actions based on task type (Schedule, Follow Up, etc.)
3. **Cleanup Discipline:** Delete completed tasks regularly to keep list manageable
4. **Thread vs Batch:** Always use `thread modify` for label removal to catch all messages
5. **Task Consolidation:** Merge duplicates immediately to avoid confusion

## Next Session
User mentioned moving to a "new feature" after taking these notes.
