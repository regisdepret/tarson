# Inbox Zero Checklist (READ THIS EVERY TIME!)

## Before Processing ANY Emails:

1. ✅ Read `rules/inbox_zero.md` to refresh rule patterns
2. ✅ Fetch all inbox emails (Gmail + iCloud)
3. ✅ Check EACH email against rules BEFORE presenting
4. ✅ Apply rule-based actions first:
   - Maisfy (no-reply@mailmais.com.br) → AUTO_DELETE
   - OneDrive memories (photos@onedrive.com) → Extract photos, show, then delete
   - Kraken BTC buys (noreply@kraken.com, "You bought BTC") → AUTO_DELETE
   - Etc. (see full rules file)

## When Presenting Emails:

1. ✅ ONE email per message (never batch multiple)
2. ✅ Extract ALL data from email (HTML decode if needed)
3. ✅ Use Telegram inline buttons (not text buttons)
4. ✅ Wait for user action before next email

## After User Clicks Button:

1. ✅ Execute the action silently
2. ✅ Delete the Telegram button message
3. ✅ Present next email immediately (no acknowledgment)

## Common Mistakes to AVOID:

- ❌ Presenting Maisfy emails individually (should auto-delete per rule)
- ❌ Not extracting OneDrive memory photos before deletion
- ❌ Sending acknowledgment messages like "Done" or "🗑️" (just execute and move on)
- ❌ Not checking rules before processing
- ❌ Deleting emails that should be tracked
- ❌ Not creating tasks for tracked emails

## Remember:

**READ THE RULES FILE FIRST. EVERY. SINGLE. TIME.**
