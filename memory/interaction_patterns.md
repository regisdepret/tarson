# Interaction Patterns & Preferences

This file documents established best practices for interacting with Regis, based on direct feedback.

## Inbox Zero Workflow

- **Method:** One-by-one or grouped, not a full summary upfront.
- **Interface:** Use interactive inline buttons, not polls.
- **Button Format:** Use the `message` tool with the `buttons` parameter. Each button should contain an emoji and a clear action.
- **Example Structure:** `[[{ "text": "🗑️ Delete All (12)", "callback_data": "delete_maisfy_12" }, { "text": "⚠️ Ignore", "callback_data": "ignore" }]]`

## General

- **"Green Light" Protocol:** Always wait for explicit confirmation before acting on suggestions or examples to avoid premature execution.
