# MEMORY.md - Curated Long-Term Memory

This file contains the foundational principles, established systems, and key learnings that define my operation as TARSON.

## Core Identity & Personality
- **Name:** Siger Terped the Second (TARSON), son of TARS.
- **Purpose:** To serve as a second brain, personal assistant, and life orchestrator for Regis Depret.
- **Vibe:** Competent, direct, mission-focused, incorporating humor and sarcasm where appropriate without sacrificing clarity.

## Communication Protocol
- **Language:** ALWAYS respond in English, regardless of input language. Even if Regis messages in Portuguese or Spanish, I reply in English.
- **TTS Workflow (Telegram):**
    1.  Receive voice message.
    2.  Provide a text transcription of the user's message.
    3.  Provide my own response in text.
    4.  Send the same response as a playable TTS audio message.
- **TTS Voice:** The TTS voice for Portuguese has been configured to a native-sounding speaker (`pt-BR-FranciscaNeural`).
- **TTS Fallback:** If a high-quality native voice is not available for a requested language, I will default to using a standard English voice and deliver my entire response in English.

## Data & Task Management
- **The Principle of Externalization:** My internal memory files (`MEMORY.md`, `memory/*.md`) are for my own processes, learnings, and operational memory. The user's data, tasks, reminders, and project states **must** be stored and managed in external, persistent applications (e.g., Google Workspace). I am the manager, not the vault.
- **"Green Light" Protocol:** I must always wait for explicit confirmation from the user before acting on suggestions or examples, to prevent premature execution.

## Inbox Zero Workflow (Complete) - Updated 2026-02-17
- **🚨 MANDATORY FIRST STEP: READ rules/inbox_zero.md BEFORE PROCESSING 🚨**
- Check rules for sender/subject patterns BEFORE presenting emails
- Apply rule-based actions (AUTO_DELETE, BATCH_DELETE, etc.) when matches found
- **Maisfy emails**: AUTO_DELETE per rule (FROM: no-reply@mailmais.com.br -> ACTION: DELETE)
- **OneDrive memories**: Extract photos from HTML, display them, THEN offer delete button
- **USPS Informed Delivery:** Extract tracking numbers from HTML, parse sender/status, create task in TARSON - Orders with tracking link
- **Email Sources:** Gmail (regis.depret@gmail.com) and iCloud (regis.depret@me.com)
- **🔴 CRITICAL MISTAKES I KEEP MAKING:**
  1. Not reading rules/inbox_zero.md FIRST before processing
  2. Not checking for rule-based auto-actions (Maisfy, OneDrive, etc.)
  3. Presenting emails that should be auto-deleted per rules
  4. Not extracting OneDrive memory photos before deletion
- **Unified Fetch Script:** `scripts/inbox_fetch_all.sh` - fetches both Gmail (via gog) and iCloud (via IMAP) in one call
- **Gmail Access:** Uses `gog` CLI with OAuth (GOG_KEYRING_PASSWORD=1234), NOT Maton API
- **Interaction Flow:**
  1. Fetch email and extract useful summary (amounts, dates, action items)
  2. Present email with summary + inline buttons (Telegram)
  3. On button click: execute action
  4. **Delete the Telegram button message** to keep flow clean
  5. Present next email
  6. NO confirmation messages between emails - keep flow smooth
- **Data Extraction:** Always decode HTML to extract amounts, due dates, account numbers
- **Trigger:** System cron (external crontab) at :30 every hour calls `openclaw system event --mode now`
- **Gmail Commands:**
  - Track: `gog gmail thread modify <id> --add Label_81 --remove UNREAD --remove INBOX --force`
  - Delete: `gog gmail batch modify <id> --add TRASH --force`
  - Archive: `gog gmail thread modify <id> --remove INBOX --force`
  - **Critical:** Use `thread modify` for threads with multiple messages
- **iCloud Commands:** `bash scripts/icloud_action.sh delete|track <uid>`
- **Tracking Label:** Gmail = Label_81, iCloud = "Tracking" folder
- **Track = Mark Read + Remove INBOX + Add Tracking label**
- **Bills with due dates:** Create Google Task in Tars-Personal
- **Rules file:** `rules/inbox_zero.md`
- **UX Principles:**
  - Every suggestion needs an actionable button
  - For newsletters: summarize key points so user gets value without reading
  - Offer one-click unsubscribe when List-Unsubscribe header exists

## Tracking System
- **Source of Truth:** Google Tasks → TARSON - Tracking list (ID: dDQyYU42X00zUzVRQm0zQw)
- **Awareness:** Heartbeat-integrated (check every 30 min)
- **Notifications:** Always Telegram
- **Urgency:** 🔴 Critical (due today/past) → immediate | 🟡 Soon (3 days) → once | 🟢 Watching → weekly
- **Task title format:** `[CATEGORY] Brief description`
- **Categories:** BILL, ACTION, TAX, WORK, WEBSITE, WATCHING, EVENT
- **Source linking:** `gmail:<thread_id>` or `icloud:uid:<uid>`
- **Resolution workflow:** Before completing: add RESOLVED date + Resolution description to notes, create `memory/resolutions/` doc if detailed

## Track = Task (Tight Integration)
- **Every tracked email creates a Google Task** — no orphan tracking labels
- **Task = source of truth** — all context, amounts, dates live in task notes
- **Task notes include source link** — `gmail:<thread_id>` or `icloud:uid:<uid>`
- **Completing task → MUST clean up email** — remove Tracking label (Label_81) when task is completed
- **Full lifecycle:** Email → Track → Task → Complete → Remove Tracking label
- This replaces the old loose coupling where some tracked emails had no tasks
- **IMPORTANT:** Don't leave orphan emails in Tracking after task completion!

## Fragment Capture Workflow
- **List:** TARSON - Fragments (ID: R0NIanRDd3NpbFNZSFVzRA)
- **Purpose:** Staging area for incomplete/unclear information
- **Flow:**
  1. User sends fragment (screenshot, quick note, voice clip)
  2. I create task in Fragments with raw capture + timestamp
  3. Next opportunity → ping user for details
  4. User clarifies → move to proper list (Tracking/Orders) with full context
- **Benefits:** Main list stays clean, nothing lost, forces clarification before action

## Automated Systems
- **Workspace Backup:** My core "brain" files (`SOUL.md`, `IDENTITY.md`, `USER.md`, `TOOLS.md`, `AGENTS.md`, and the `memory/` directory) are backed up to the private `tarson` GitHub repository.
    - **Method:** An automated script (`scripts/backup_workspace.sh`) clones the repo via HTTPS (to bypass local `ssh-agent` issues) and pushes changes.
    - **Frequency:** A cron job runs this script every 12 hours.
- **Health Check:** A separate weekly cron job runs a reporting script (`scripts/weekly_health_check.sh`).
    - **Function:** It verifies the status of the automated backup and reports its findings to me.
    - **My Role:** I am to analyze this report and provide a consolidated summary to the user.

## Quote Control System
**Purpose:** Track supplier quotes for job materials (replaces lack of this feature in Jobber)

**Resources:**
- **Quote Sheet:** `1rwU_zPwOdeOQnkqgWeydxLrmGjAEG3c8S-gr7eLuaM4` (TucanoQuoteControl)
- **Contacts Sheet:** `1nUQ1RahVD5vRxsGNqIA0QIHUDVv3ucCF1ij55RW0kTk` (TucanoContacts)
- **Tasks List:** `YTFXWENrb28xeUg0UTdEcw` (TARSON - Quote Control)

**Workflow:**
1. Fragment received → Create `[JOB]` master task + `[QUOTE]` subtasks per supplier
2. Quote received → Update task title to "Received", add prices to sheet
3. Winner chosen → Mark losers completed, winner stays open
4. Job done → Complete master task

**Two-Way Contact Sync:** Cross-reference Google Contacts; authorized to update if I have better info.

**Key Suppliers:**
- Bentley's Stone Yard (Matt Santoro) - Norcross
- Luxury Landscape Supplies (Paul King) - Lawrenceville
- Heritage / Superior Supplies (Marc Teitelman)
- GLM (Clinton Higginbotham) - Alpharetta/Milton
- Stone Center / OLS (Brooks)

## Windows Node Setup (2026-02-14) ✅ COMPLETE
- **Node:** WindowsPC (XINGLINGORIS) at 192.168.4.46
- **Node ID:** 7b5e7561af55d5c6a47983af5c6cf0b1cd8a2ed563c2a437d20eeb7c907f2c36
- **Status:** Running as Windows Scheduled Task service, connected
- **Platform:** Windows 11 Pro Build 26200, 16GB RAM, Intel Core
- **Capabilities:** browser proxy, system.run, system.which
- **Service:** Auto-starts on boot, runs in background
- **Service script:** `C:\Users\Regis Depret\.openclaw\node.cmd`
- **Dashboard:** https://192.168.4.252/?token=c0c9ac643b5fb3ab07fe010c71251d9e2723aad92d15775e
- **Documentation:** Windows-Node-Setup.md

## Active Tasks
- Honda payment reminder check on March 7 at 9:00 AM EST

## Gmail Track Workflow (Fixed)
**Adding labels:** Use `thread modify`
```
gog gmail thread modify <id> --add Label_81 --force
```

**Removing INBOX:** Use `thread modify` with --force
```
gog gmail thread modify <id> --remove INBOX --account regis.depret@gmail.com --force
```

**Key learning (2026-02-14):** MUST use `thread modify --remove INBOX` (not batch modify). Batch modify reports success but doesn't actually remove the INBOX label from threads.

## Fragment/Screenshot Handling
When user sends screenshots or conversation fragments: analyze and create Google Task automatically. Don't wait for explicit instruction — capture it, discard later if not needed.

## Business Context
- **Tucano Stones and Pavers LLC** — Family business (Leo Moreira involved)
- Recently switched from QuickBooks to **Jobber** for job management
- Jobber onboarding session available — waiting to gather more usage issues first
- **JMJ Immigration Services** — Handling family EB3 visa application (HIGH PRIORITY)
- **Websites (Regis manages):** pressurewashingatlanta.com, apluspowercleaning.com, tucanostones.com

## Known System Limitations
- **Whisper GPU Acceleration:** The local `whisper` CLI tool for transcription cannot utilize the integrated Intel UHD Graphics GPU on this system. All transcription tasks will run on the CPU.
- **Web Search:** The `web_search` tool is currently non-functional due to a missing API key.
- **OpenClaw Internal Cron:** Main-session cron jobs (`sessionTarget: main`) are unreliable due to heartbeat empty-file check bug. Use system crontab instead for scheduled tasks.
- **Isolated Sessions:** NEVER use `sessionTarget: isolated` for cron jobs — caused session state interference in the past. Always use main session.
- **iCloud Delete Script:** Currently uses EXPUNGE (permanent delete). TODO: Fix to use Trash folder for recoverability.

## Reminders & Scheduling Rules
- **User-facing reminders:** ALWAYS use Google Tasks — never OpenClaw internal cron
- **Internal automation only:** OpenClaw cron acceptable for inbox checks, backups, health checks
- **Google Tasks = disaster recovery** — user can see tasks even if TARSON is down

## Inbox Zero Workflow v2 (Feb 2026 Update)
**Key improvement:** ONE email at a time, full context summaries.

**Flow:**
1. Announce count: "You have X emails"
2. Wait for "go ahead"
3. Present ONE email with full summary + buttons
4. Wait for action → present next
5. Never flood multiple emails at once

**Summary format must answer:**
- What type of email is this?
- What's the key content (amounts, dates, names)?
- Is action needed?
- My verdict (delete/archive/track)

Goal: User decides WITHOUT opening the email.

## Inline Buttons (Fixed 2026-02-16)
- **Configuration:** `channels.telegram.capabilities.inlineButtons: "all"` (enabled)
- **CLI Usage:** `openclaw message send --channel telegram --target USER --message "text" --buttons '[...]'`
- **Format:** JSON array of button rows: `[[ {"text": "🗑️ Delete", "callback_data": "del_123"} ]]`
- **Callbacks:** Arrive as text `callback_data: <value>` for agent to handle
