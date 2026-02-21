# Inbox Zero Rulebook

This file contains the rules TARSON uses to triage and process incoming emails.

## INTERACTION BEHAVIOR

### Flow (STRICT: One-by-One)
1. Fetch all unread emails from both Gmail and iCloud
2. Present **ONE email** with summary + inline buttons
3. **STOP AND WAIT** for user's button click
4. On callback: execute action silently
5. Present the **NEXT email** (one only)
6. **STOP AND WAIT** again
7. Repeat until all emails processed
8. Send brief "✅ Inbox zero" when done

**NEVER send multiple email messages in a row.** Always wait for user action between each email. This is not optional — bulk sending defeats the purpose of interactive triage.

**NO ACKNOWLEDGMENT MESSAGES.** Don't reply "🗑️" or "Done" or "Tracked". Just execute the action silently, delete the button message, and present the next email. Only the email cards matter.

### Presenting Emails
- **ONE EMAIL PER MESSAGE** — never batch/bulk multiple emails together
- Extract ALL available data (amount, due date, account number, etc.)
- For HTML-only emails, decode and parse the HTML to get details
- Never say "details not available" if they're extractable
- Keep summaries concise but complete

### Telegram Inline Buttons (CRITICAL)
**ALWAYS use the `buttons` parameter in the message tool**, NOT slash commands as text.

Example correct usage:
```json
{
  "action": "send",
  "channel": "telegram", 
  "to": "98960672",
  "message": "📧 **Sender Name**\n**Subject Line**\n\nSummary of content here",
  "buttons": [[
    {"text": "🗑️ Delete", "callback_data": "del_THREADID"},
    {"text": "📁 Archive", "callback_data": "arc_THREADID"}
  ]]
}
```

**NEVER** write buttons as text like:
```
🗑️ /del_abc123
📁 /arc_abc123
```
This is broken UX — the user can't tap these.

### Button Callback Handling
When receiving a callback like `del_19c479888cdfe50a`:
- Parse the action prefix (del/arc/trk/idel/iarc)
- Extract the ID
- Execute the appropriate command
- **Delete the button message** using the Telegram messageId I saved when sending

### Message ID Tracking (CRITICAL)
When sending an email with buttons, the response includes `messageId` (e.g., "711").
I MUST remember this ID so I can delete the message after the user clicks.

Store mapping: `email_thread_id → telegram_message_id`

When callback arrives, look up the telegram_message_id and delete it:
```json
{"action": "delete", "channel": "telegram", "messageId": "711"}
```

### Tracking Behavior
- Emails that will become threads (replies expected) → Track in Gmail label
- Tasks with due dates → Also create Google Task
- Track = Mark Read + Add Tracking label — **KEEP IN INBOX** (do NOT remove INBOX label)
- Rationale: User prefers to see tracked items in inbox for visual reference

### Button Actions
- **del_ID** (Gmail): Move to trash via `gog gmail batch modify ID --add TRASH --force`
- **arc_ID** (Gmail): Archive via `gog gmail thread modify ID --remove INBOX --force`
- **trk_ID** (Gmail): **USE NATIVE TASK AUTOMATION** — call `track_email.sh`:
  ```bash
  export GOG_KEYRING_PASSWORD=1234
  export NODE_PATH=/home/regis/.nvm/versions/node/v22.22.0/lib/node_modules
  bash scripts/track_email.sh track <thread_id> <CATEGORY> "<title>"
  ```
  Auto-determine CATEGORY and title from email content (see Auto-Category Logic below).
  This creates a natively-linked Google Task in TARSON-Tracking + applies Label_81.

- **snz_ID** (Gmail): **USE NATIVE TASK AUTOMATION** — call `track_email.sh` with snooze:
  When user clicks Snooze, first present snooze date buttons:
  ```
  [Tomorrow] [In 3 days] [Next week] [Pick date]
  ```
  Then call:
  ```bash
  bash scripts/track_email.sh snooze <thread_id> <CATEGORY> "<title>" <YYYY-MM-DD>
  ```
  Applies Tracking/Snooze label (Label_3397993892725869791). Email stays in inbox.

- **idel_UID** (iCloud): Legacy — iCloud now forwards to Gmail, should not appear
- **iarc_UID** (iCloud): Legacy — iCloud now forwards to Gmail, should not appear

### Auto-Category Logic (for track_email.sh calls)
Determine CATEGORY from email content using these rules (first match wins):

| CATEGORY  | Signals |
|-----------|---------|
| BILL      | Subject/body: invoice, payment, statement, due, balance, amount due; From: utility, bank, insurance, subscription service |
| TAX       | Subject: 1099, W-2, W2, tax document, giving statement, IRPF, fiscal |
| ORDER     | Subject: order confirmed, shipped, tracking, delivery; From: Amazon, UPS, FedEx, USPS |
| QUOTE     | Subject: quote, estimate, proposal; From: supplier, vendor |
| WORK      | Business emails, client issues, supplier follow-ups, job-related |
| EVENT     | Subject: invitation, register, ticket, event, webinar |
| WAITING   | Emails awaiting a reply or confirmation from someone else |
| ACTION    | Requires a specific action but doesn't fit other categories |
| WATCHING  | No immediate action needed — monitoring only |

**Title format:** Strip email junk (Re:, Fwd:, [EXTERNAL], etc.), keep the core subject + key detail (amount, name, date).
Example: "Sawnee EMC — $201.27 due Feb 20" not "RE: [EXTERNAL] Your February Statement is Ready"

## CORE PRINCIPLE: Smart Auto-Delete
Auto-delete rules are NOT blind. I always analyze the email first.
If context is unclear or the email doesn't match the expected pattern
(e.g., account issue vs engagement spam), I bring it to the user's attention.

## CORE PRINCIPLE: Actionable Buttons
If I suggest an action in my analysis (track, save, add to calendar, etc.),
I MUST provide a button for that action. Don't recommend without enabling.

## CORE PRINCIPLE: Group Similar Emails
If multiple emails from the same sender with similar content type exist,
group them together for batch action instead of presenting one by one.

## CORE PRINCIPLE: One-Click Unsubscribe
Check for List-Unsubscribe header in marketing emails. If present, offer
an "Unsubscribe" button that handles it automatically.

## CORE PRINCIPLE: Not Resolved Until Confirmed
"I'll do it" or "handling it" is NOT resolved. Track until:
- User explicitly confirms it's done, OR
- Email confirmation of completion arrives
Don't assume promises = resolution.

## CORE PRINCIPLE: Handle Threads Together
Gmail groups messages by thread_id. When processing:
- Check if multiple inbox emails share the same thread_id
- Present the whole thread as one item, not individual messages
- One action (track/delete) applies to all messages in the thread
- **Use `gog gmail thread modify` NOT `batch modify`** - batch only affects one message, thread affects all messages in the thread

## CORE PRINCIPLE: Archive Over Delete for Trackable Items
For emails that could be tracked (customer issues, business matters):
- Prefer archive over delete - issues can resurface
- Offer "✅ Solved (archive)" button for resolved items
- Archive keeps history accessible; delete loses it forever
- Only suggest delete for true noise (marketing, spam, duplicates)

## CORE PRINCIPLE: INBOX is the Source of Truth
**Every tracked email MUST be in INBOX. No exceptions.**
- Track = `--add Label_81 --remove UNREAD` + email stays in INBOX
- If an email has Label_81 but is NOT in INBOX → immediately restore it: `--add INBOX`
- INBOX is what Regis sees daily. If it's not there, it doesn't exist for him.
- The email lives in both Inbox AND Tracking label until the task is resolved/completed
- When completing a task → THEN remove INBOX + Label_81 (clean up the email)

## CORE PRINCIPLE: Track = Native Task (Source of Truth)
Every tracked email MUST create a task via `scripts/track_email.sh` (NOT via `gog tasks add`):
- Uses Playwright to click Gmail's native "Add to Tasks" → task gets a real Gmail deep-link
- Task title: `[CATEGORY] Brief description` (auto-enriched by script)
- Task notes include: `source: gmail:<thread_id>`, `link: https://mail.google.com/mail/u/0/#all/<thread_id>`
- Set due date if applicable (required for snooze)
- **Task is the source of truth** — richer info lives here, not in email labels
- When task is completed → use gmail link from notes to archive/delete the source email
- This creates a full lifecycle: Email → Track (native task) → Task → Complete → Clean up email
- Auth required: `memory/gmail_auth_state.json` must exist (run `gmail_auth_setup.js` if expired)

## Rule Format
Rules are processed from top to bottom. The first rule that matches an email is the one that is applied.

`CONDITION: value -> ACTION: action_name, PARAM: param_value`

### Conditions
- `FROM`: Matches the sender's email address.
- `SUBJECT`: Matches keywords in the subject line.
- `DEFAULT`: A catch-all for any email that doesn't match a previous rule.

### Actions
- `NOTIFY_URGENT`: Flag this for immediate attention at the top of the report.
- `SUMMARIZE`: The default action. Provide a summary.
- `ARCHIVE`: Suggest archiving immediately.
- `DELETE`: Suggest deleting immediately.

---

## My Rules

# Serasa / SABESP Water Bills (Brazil)
# Story: Old water account from when Regis lived in São Paulo (moved out 2015).
#        Name still on bill, current resident Natalino fails to pay sometimes.
#        Regis forwards these to Natalino and tracks until 6+ months clear.
# Rule: Forward to Natalino, track. Goal: name removed from account.
FROM: serasa.comunicado@contato.serasa.com.br, SUBJECT: "SABESP" OR "débitos" -> ACTION: FORWARD_TO_NATALINO_AND_TRACK

# Tax Documents (Giving Statements, 1099s, W2s, etc.)
# Story: Tax docs need to be collected and sent to accountant.
# Rule: Track until sent to accountant. Helps remember to handle taxes on time.
SUBJECT: "tax document" OR "giving statement" OR "1099" OR "W-2" OR "W2" -> ACTION: TRACK_FOR_ACCOUNTANT

# JMJ Immigration Services
# Story: Handling family visa application. High priority, always needs attention.
# Rule: Flag as urgent, suggest tracking for any action items.
FROM: admin@jmjimmigration.com -> ACTION: NOTIFY_URGENT

# Example Urgent Rule
FROM: boss@example.com -> ACTION: NOTIFY_URGENT

# Example Newsletter Rule
FROM: newsletter@company.com -> ACTION: ARCHIVE

FROM: no-reply@mailmais.com.br -> ACTION: DELETE

# OneDrive Memories
# Story: These are heartwarming and should be experienced, not managed.
# Rule: Extract thumbnail URLs with tempauth tokens from email HTML, download via curl, display in chat.
#       Then await confirmation to delete email + button message + photo messages + local files.
# 
# METHOD (proven working 2026-02-09, 2026-02-10, 2026-02-13, 2026-02-19):
# 1. Get email as JSON: GOG_KEYRING_PASSWORD=1234 gog gmail thread get <id> --account regis.depret@gmail.com --json
# 2. Extract HTML body: jq -r '.thread.messages[0].payload.parts[] | select(.mimeType == "text/html") | .body.data'
# 3. Decode base64 (URL-safe): tr '_-' '/+' | base64 -d > email.html
# 4. Extract photo URLs: grep -oP 'https://my\.microsoftpersonalcontent\.com/[^"<>\s]+' email.html
# 5. Download with curl: curl -sL "$url" -H "User-Agent: Mozilla/5.0" -o "memory_$num.jpg"
# 6. Send photos to Telegram using message tool with media parameter
# 7. TRACK MESSAGE IDs: Save all photo message IDs returned from message tool
# 8. On delete: trash email, delete button message, DELETE ALL PHOTO MESSAGES, rm -rf local directory
#
# Note: The microsoftpersonalcontent URLs contain tempauth tokens valid for ~30 days.
#       Always send images FIRST, then present summary with buttons.
#       CRITICAL: Delete photo messages from Telegram when deleting the email!
FROM: photos@onedrive.com -> ACTION: SHOW_MEMORIES

# USPS Informed Delivery
# Story: Daily digest of incoming mail. Need to see images to check for important items.
# Rule: Extract mail images, ANALYZE them first (sender, content type, priority), then show with a summary and recommendation. Delete after review.
FROM: USPSInformeddelivery@email.informeddelivery.usps.com -> ACTION: ANALYZE_AND_SHOW_MAIL

# Rewards/Points Expiring
# Story: Need to see if worth acting before they expire.
# Rule: Summarize what's expiring, the amount/value, and by when. User decides, then delete.
SUBJECT: "expire" OR "expiring" OR "points" AND "expire" -> ACTION: SUMMARIZE_VERIFY_DELETE

# Bank of America Notifications
# Story: Need to verify transactions are correct.
# Rule: Summarize key details (balance, deposits, transactions), user confirms then delete.
FROM: onlinebanking@ealerts.bankofamerica.com -> ACTION: SUMMARIZE_VERIFY_DELETE

# Webinar/Event Invitations
# Story: These need analysis to decide if worth attending.
# Rule: Summarize (topic, date/time, relevance), offer actions (Add to Calendar, Skip).
SUBJECT: "webinar" OR "invitation" OR "event" OR "register" -> ACTION: ANALYZE_EVENT_DECIDE

# Feedback/Survey Emails
# Story: Survey requests are noise.
# Rule: Auto-delete (but check context first per core principle).
SUBJECT: "feedback" OR "survey" OR "review your" -> ACTION: AUTO_DELETE

# Duolingo Engagement Emails
# Story: Gamification/streak reminders are noise.
# Rule: Auto-delete.
FROM: hello@duolingo.com -> ACTION: AUTO_DELETE

# Honda Payment Reminders
# Story: Need tracking until due date (7th of month) to confirm payment.
# Rule: Create calendar reminder for 7th of each month to check payment. After confirmation, delete emails.
FROM: do_not_reply@hondafinancialservices.com -> ACTION: TRACK_UNTIL_DUE_DELETE

# Marktechpost AI Newsletter
# Story: AI industry news with occasional valuable insights.
# Rule: Summarize key news, highlight anything actionable, offer options to save links or act. Then delete.
FROM: marktechpost-newsletter@mail.beehiiv.com -> ACTION: SUMMARIZE_HIGHLIGHT_DELETE

# Cultura Builder Community Updates
# Story: Vibe coding community with valuable insights and tips.
# Rule: Analyze content, extract interesting insights, present summary with recommendations.
#       Offer to save interesting links to google.com/saved. Then delete email.
FROM: hello@supersend.culturabuilder.com -> ACTION: ANALYZE_SUMMARIZE_RECOMMEND

# Google Search Console Alerts
# Story: Website indexing issues. Regis handles websites and forwards to appropriate Claude session.
# Rule: Track for follow-up. Applies to pressurewashingatlanta.com, apluspowercleaning.com, tucanostones.com
FROM: sc-noreply@google.com -> ACTION: TRACK_FOR_WEBSITE_REVIEW

# Website Forwards from A Plus Power Cleaning / Tucano Stones
# Story: Leo or team forwards website issues. Same handling as direct Search Console alerts.
FROM: apluspowercleaning@gmail.com, SUBJECT: "Search Console" OR "indexed" -> ACTION: TRACK_FOR_WEBSITE_REVIEW
FROM: Info@tucanostones.com, SUBJECT: "Search Console" OR "indexed" -> ACTION: TRACK_FOR_WEBSITE_REVIEW

# Google Business Profile Reports
# Story: Monthly performance reports for Tucano Stones & Pavers.
# Rule: Summarize key metrics in weekly report, then delete.
FROM: businessprofile-noreply@google.com -> ACTION: SUMMARIZE_WEEKLY_DELETE

# Coinbase - Recurring Buy Confirmations
# Story: These are handled by another robot. Don't touch them.
# Rule: IGNORE completely (don't delete, don't mark as read).
#       BUT if more than 4 pile up, alert user that the other robot may be stuck.
FROM: no-reply@info.coinbase.com, SUBJECT: "Your recurring buy for" -> ACTION: IGNORE_UNLESS_PILING_UP, THRESHOLD: 4

# Kraken - BTC Purchase Confirmations
# Story: Recurring auto-buys, no need to see each one.
# Rule: Auto-delete "You bought BTC" emails. Other Kraken emails pass through for review.
FROM: noreply@kraken.com, SUBJECT: "You bought BTC" -> ACTION: AUTO_DELETE

# Kraken - Deposit Confirmations
# Story: Deposit confirmations are informational, same as auto-buys.
# Rule: Auto-delete deposit confirmations.
FROM: noreply@kraken.com, SUBJECT: "You deposited funds" -> ACTION: AUTO_DELETE

# Kraken - Promotional Emails
# Story: Promos are noise but might have useful info.
# Rule: Summarize in weekly report, then delete.
FROM: no-reply@email.kraken.com -> ACTION: SUMMARIZE_WEEKLY_DELETE

# Amazon Order Emails
# Story: Orders need tracking until delivered. Kindle/instant deliveries don't need tracking.
# Rule: For physical orders, create a task in "TARSON - Orders" with item, date, expected delivery.
#       Update task as status changes. Mark complete or delete when delivered.
#       For instant deliveries (Kindle, digital), note in weekly report and delete email.
FROM: digital-no-reply@amazon.com -> ACTION: LOG_INSTANT_DELIVERY_DELETE
FROM: ship-confirm@amazon.com -> ACTION: TRACK_ORDER_IN_TASKS
FROM: shipment-tracking@amazon.com -> ACTION: UPDATE_ORDER_TASK

# Google Calendar - No Events
# Story: Now that TARSON exists, these are a self-audit for me, not for the user.
# Rule: Verify calendar awareness, then delete silently. No user action needed.
SUBJECT: "no events scheduled" -> ACTION: SELF_AUDIT_DELETE

# Security Code Emails
# Story: Login codes are time-sensitive and a potential security signal.
# Rule: Notify immediately for awareness, then suggest deletion. High priority.
SUBJECT: "code" -> ACTION: NOTIFY_URGENT, SUGGEST: DELETE

# GA Annual Registration
# Story: Unofficial companies trying to sell a service for something I can do myself.
# Rule: Check if a task already exists. If not, create a task to handle it manually before the deadline. Archive the email.
SUBJECT: "Annual Registration" -> ACTION: CHECK_THEN_CREATE_TASK

# Maisfy Course Notifications
# Story: Bulk notifications for a course.
# Rule: Group, count, notify, then suggest bulk deletion. Low priority.
FROM: no-reply@mailmais.com.br -> ACTION: BATCH_DELETE

# Default Catch-All
DEFAULT -> ACTION: SUMMARIZE
