# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## 🔧 Helper Scripts (CHECK FIRST!)

**Before manually constructing complex commands, check `SCRIPTS.md` for existing helper scripts.**

Common scripts:
- `bash scripts/gmail_search.sh "<query>"` - Search Gmail
- `bash scripts/email_from_sender.sh <name>` - Find emails from sender
- `bash scripts/quote_lookup.sh <job>` - Search Quote Control
- `bash scripts/tracking_check.sh` - Check tracking tasks
- `bash scripts/gmail_thread_details.sh <id>` - Get thread details

See `SCRIPTS.md` for full list.

## TTS - Text-to-Speech

- **Engine:** `edge-tts` (installed via pipx)
- **Preferred Voice:** `en-US-EricNeural` (selected by user 2026-02-21)

### Old Config (for reference)
- **Previous Engine:** OpenClaw built-in
- **Previous Voice:** `pt-BR-FranciscaNeural`

## TTS Comparison (TODO)
- **Current:** OpenClaw built-in TTS (configured with `pt-BR-FranciscaNeural`)
- **Evaluate:** Edge TTS (`edge-tts` Python package) — free, high-quality, many voices
  - Install: `pip install edge-tts`
  - CLI: `edge-tts --text "Hello" --voice en-US-AriaNeural --write-media out.mp3`
  - List voices: `edge-tts --list-voices`

## Hardware & Acceleration

- **GPU:** Intel Corporation Alder Lake-N [UHD Graphics] (Integrated)
- **Whisper (Transcription):** The installed version does not support acceleration on the integrated Intel GPU. It will run on the CPU only.

## gws (Google Workspace CLI) - Gmail & Tasks
- **CLI:** Official Google Workspace CLI (v0.9.1)
- **Binary:** `/home/regis/.npm-global/bin/gws`
- **Credentials:** `~/.config/gws/credentials.json`
- **Env var:** `GOOGLE_WORKSPACE_CLI_CREDENTIALS_FILE=$HOME/.config/gws/credentials.json` (set in `~/.bashrc`)
- **Scopes:** gmail.modify, tasks, calendar, drive, spreadsheets

### Gmail Commands
- **Trash emails:** `gws gmail users threads modify --params '{"userId":"me","id":"<THREAD_ID>"}' --json '{"addLabelIds":["TRASH"]}'`
- **Archive emails:** `gws gmail users threads modify --params '{"userId":"me","id":"<THREAD_ID>"}' --json '{"removeLabelIds":["INBOX"]}'`
- **Search threads:** `gws gmail users threads list --params '{"userId":"me","q":"in:inbox","maxResults":50}'`
- **Get thread details:** `gws gmail users threads get --params '{"userId":"me","id":"<THREAD_ID>","format":"full"}'`

### Tasks Commands
- **List tasks:** `gws tasks tasks list --params '{"tasklist":"<LIST_ID>","maxResults":100,"showCompleted":false}'`
- **Add task:** `gws tasks tasks insert --params '{"tasklist":"<LIST_ID>"}' --json '{"title":"...","notes":"...","due":"2026-03-14T00:00:00.000Z"}'`
- **Complete task:** `gws tasks tasks patch --params '{"tasklist":"<LIST_ID>","task":"<TASK_ID>"}' --json '{"status":"completed"}'`
- **Delete task:** `gws tasks tasks delete --params '{"tasklist":"<LIST_ID>","task":"<TASK_ID>"}'`

### Migration Notes
- Migrated from `gog` CLI (third-party) to `gws` (Google official) on 2026-03-10
- No more `GOG_KEYRING_PASSWORD` needed
- Simpler syntax: `gws <service> <resource> <method> --params '...'`

## iCloud Mail (IMAP)
- **Email:** `regis.depret@me.com`
- **App-specific password:** `ovay-iihc-mwgf-dnjf`
- **IMAP server:** `imap.mail.me.com:993`
- **Fetch script:** `scripts/icloud_inbox_fetch.sh`
- **Delete email:** Use IMAP `STORE <id> +FLAGS \\Deleted` then `EXPUNGE`
- **Mark as read:** Use IMAP `STORE <id> +FLAGS \\Seen`
- **Important:** Use `BODY.PEEK[]` when fetching to avoid marking as read

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.
