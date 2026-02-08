# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## Hardware & Acceleration

- **GPU:** Intel Corporation Alder Lake-N [UHD Graphics] (Integrated)
- **Whisper (Transcription):** The installed version does not support acceleration on the integrated Intel GPU. It will run on the CPU only.

## gog (Google Workspace CLI) - Gmail
- **Keyring Password:** `1234` (set via `GOG_KEYRING_PASSWORD` env var)
- **Account:** `regis.depret@gmail.com`
- **Trash emails (delete):** `GOG_KEYRING_PASSWORD=1234 gog gmail batch modify <ids> --account regis.depret@gmail.com --add TRASH --force`
- **Archive emails:** `GOG_KEYRING_PASSWORD=1234 gog gmail batch modify <ids> --account regis.depret@gmail.com --remove INBOX --force`
- **Note:** `batch delete` (permanent) requires broader OAuth scope; use `--add TRASH` instead

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
