# Inbox Zero Rulebook

This file contains the rules TARSON uses to triage and process incoming emails.

## INTERACTION BEHAVIOR

### Flow
1. Present email with summary + inline buttons
2. Wait for button click
3. On click: execute action silently, delete the message, present next email
4. NO confirmation messages between emails - keep the flow smooth

### Presenting Emails
- Extract ALL available data (amount, due date, account number, etc.)
- For HTML-only emails, decode and parse the HTML to get details
- Never say "details not available" if they're extractable
- One email = one message with inline buttons

### Tracking Behavior
- Emails that will become threads (replies expected) → Track in Gmail label
- Tasks with due dates → Also create Google Task
- Track = Mark Read + Remove INBOX + Add Tracking label

### Button Actions
- Delete: Move to trash, delete message, next email
- Track: Add label + remove inbox + mark read, delete message, next email  
- Archive: Remove inbox only, delete message, next email
- Skip: Delete message, next email

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

## CORE PRINCIPLE: Track = Mark as Read + Remove from Inbox
When tracking an email (adding to Tracking label/folder):
- Also mark the email as read (remove UNREAD label)
- Also remove from inbox (remove INBOX label)
- Tracking means it's been triaged and moved — not visible in inbox anymore
- The email lives in "Tracking" view only until resolved

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
# Rule: Extract images, display them in chat, then await confirmation to delete both the chat messages and the original email.
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
