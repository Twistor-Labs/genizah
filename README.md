# Genizah

Genizah is a free Claude Code plugin and Agent Skill that frees disk space on Mac, Windows and Linux without losing what matters. It is for anyone whose disk is full and who wants their coding agent to clean up carefully: it measures first, asks before anything personal, and puts personal files in the Trash instead of deleting them.

Install in Claude Code with `/plugin marketplace add Twistor-Labs/genizah` then `/plugin install genizah@genizah`, or in other agents with `npx skills add Twistor-Labs/genizah`.

Site: [genizah.twistorlabs.com](https://genizah.twistorlabs.com). From [Twistor Labs](https://twistorlabs.com). Complexity, untwisted.

## What it does

- On your first run it explains itself and asks what it should never touch. That can be a folder, an app, or a kind of file like "all my videos". It remembers your answer.
- It sorts everything into Safe (rebuilds itself), Check with you, and Do not touch before anything is deleted.
- Safe items like app caches, logs and download caches go after a single yes from you.
- Personal items come one at a time, each with its size and the date it last changed, and each needs its own yes.
- Personal files go to the Trash or Recycle Bin, so you can put them back.
- Before anything important goes (photos, a phone backup, recent work, emptying the Trash) it stops and tells you what you'd lose and whether it can come back. Permanent actions need you to confirm by name.
- It checks whether you have a backup and tells you plainly if you don't.
- Separate guides for macOS, Windows and Linux cover where space hides on each system.
- It finishes with free space before and after, what was cleared, what's waiting in the Trash, and two or three tips picked for your machine.

## Install

Genizah uses the open Agent Skills format (`SKILL.md`). It was built and tested in Claude Code, which needs a paid Claude plan.

**Claude Code.** Run:

```
/plugin marketplace add Twistor-Labs/genizah
/plugin install genizah@genizah
```

**Claude desktop app or claude.ai.** Download `genizah.zip` from [Releases](https://github.com/Twistor-Labs/genizah/releases/latest), then Customize > Skills > upload. Running the cleanup needs a computer it can reach, so the desktop app with Claude Code is the better fit.

**Other agents** (Codex, Cursor, GitHub Copilot, Gemini CLI, OpenCode and more):

```
npx skills add Twistor-Labs/genizah
```

**No plugin system at all:**

1. Download this repository as a ZIP and unzip it anywhere.
2. Open the unzipped `genizah` folder in Claude Code.
3. Type: *Help me free up space on my computer.*

## How to use it

Ask in your own words. The skill loads when the request is about disk space, for example:

- "Help me free up space on my computer."
- "My startup disk is almost full."
- "My C drive is full. What can I delete?"
- "Why is System Data so big?"
- "Is it safe to clear my Downloads folder?"

Read each list before you say yes. You can stop at any time.

## What it will never touch

- Operating system folders, system snapshots, restore points and boot files
- Browser profiles: logins, passwords and history (only the cache subfolders)
- Cloud sync folders: iCloud Drive, OneDrive, Dropbox, Google Drive. Deleting there deletes everywhere
- Photo libraries and messaging app folders (clear media from inside the app instead)
- Virtual machine and container disk images
- Password manager, SSH and GPG key folders
- Anything on your own never-touch list

It never uses admin access (`sudo`, or "Run as administrator" on Windows) without asking you first and saying why. It deletes by exact path, never by wildcard.

## Before you start

Back up if you can: Time Machine on a Mac, Windows Backup or File History on Windows, Timeshift or Déjà Dup on Linux. Clearing caches is fine without a backup. Personal files are where a backup earns its keep.

Your never-touch list lives in `~/.genizah/history.md` (on Windows, `%USERPROFILE%\.genizah\history.md`). Edit it whenever you like, or tell Genizah "never touch my Projects folder" mid-cleanup.

## Why the name

For about a thousand years, the Ben Ezra synagogue in Cairo kept a storeroom called a genizah. Worn-out papers weren't burned. They were set aside. In 1896 scholars realized what had piled up in there: around 400,000 pages of letters, contracts, shopping lists and poems, saved because nobody was in a hurry to destroy them.

Genizah works the same way. Set things aside first. Erase last, and only when you're sure.

## Support

hello@twistorlabs.com

## License

[MIT License](LICENSE). Copyright 2026 Twistor Labs Private Limited. Genizah is not affiliated with Anthropic.
