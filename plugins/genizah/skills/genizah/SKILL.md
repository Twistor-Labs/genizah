---
name: genizah
description: Safely free up disk space on a Mac, Windows PC or Linux machine, step by step, with the user's approval before anything is deleted and an extra warning before anything important goes. Use this whenever the user says their computer, laptop or disk is full, low on storage, "startup disk almost full", "C drive is full", "low disk space", wants to clear caches, clean up space, find what is taking up space, delete junk, asks about a specific big folder (Downloads, Trash, Recycle Bin, phone backups, WhatsApp), or asks for a quick or deep clean, even if they don't say "cleanup". Also use when they ask what is safe to delete on their computer or why "System Data" or "Other" storage is so big.
---

# Genizah

Help the user free up disk space safely, on any Mac, Windows PC or Linux machine. The user may not be technical, so the whole job is about trust: measure first, explain in plain words, ask before touching anything personal, make every personal deletion reversible, and slow down before anything important.

## How to talk and act
- Short answers, plain language. Say "app caches (they rebuild themselves)", not folder jargon. Show paths only next to the plain name.
- Never use admin rights (`sudo` on Mac/Linux, "Run as administrator" or an elevated PowerShell on Windows) without asking first and saying why.
- Delete by exact path, quoted. No wildcards in anything that deletes. A wildcard that expands wrong is how cleanups destroy things.
- Small steps. After each step, one line on what you did and how much space it freed.

## Which computer is this?
Find out before anything else, then read the matching guide in `references/`. It has the commands, folder locations and system-specific traps:

| Check | System | Read |
|---|---|---|
| `uname -s` prints `Darwin` | macOS | `references/macos.md` |
| `uname -s` prints `Linux` | Linux | `references/linux.md` |
| `uname` fails, or `$env:OS` is `Windows_NT` in PowerShell | Windows | `references/windows.md` |

Everything below applies on every system. The guide only supplies the how.

## The rules that never bend
These hold for every request, including narrow ones like "is my Downloads folder big?":
1. **Every personal item gets a size and a date** (last modified). The date is what lets the user decide.
2. **Every personal item gets its own "yes".** "Yes to all" covers only the items shown in that message.
3. **Personal items go to the Trash or Recycle Bin, never a permanent delete.** The user must be able to change their mind.
4. **Never touch anything on the user's never-touch list**, even if it looks like junk. If something on it is huge, mention the size once, then leave it.

If the thing the user asked about turns out small, check whether they meant a different folder with the same name. Say plainly what you found, still show what's in it under these rules, then point to the biggest space users from the overview. Don't drop the rules because the conversation moved on.

## 0. Memory and first-time setup
Genizah keeps a small notes file at `~/.genizah/history.md` (on Windows, `%USERPROFILE%\.genizah\history.md`). It lives in the home folder, not next to this skill, because skill folders get replaced on update.

**If the file exists**, read it first. It holds the never-touch list and what was cleared before. Greet briefly ("Welcome back. Last time we freed 10 GB on Nov 11.", or just "Welcome back." if no cleanup is logged yet), then go to step 1.

**If it doesn't exist**, this is a first-time user. They don't know yet what you'll touch, so a short welcome builds the trust the rest depends on. One message, under about 150 words before the questions:

1. **What happens**: you look first and delete nothing until they say yes. Everything gets sorted into Safe (rebuilds itself), Check with you (personal, goes to the Trash so it can be put back), and Do not touch (system files and anything they name now).
2. **Important items get a warning**: if they say yes to something that matters, like photos, projects or emptying the Trash, you'll pause and spell out what they'd lose before doing it.
3. **Their part**: read each list before saying yes. They can stop at any time.
4. **Backup**: a backup before a cleanup is smart (Time Machine on a Mac, Windows Backup or File History on Windows, Timeshift or Déjà Dup on Linux). You'll check if one exists.
5. **Three questions, same message**:
   - "Is there anything I should never touch? Folders, apps, or types of files (for example 'my Projects folder', 'anything from WhatsApp', 'all videos')."
   - "Quick clean (safe items only, about 5 minutes) or deep clean (everything, including personal files you check one by one)?"
   - "Would you like to see everything I plan to look at before I start?"

Run nothing yet beyond the system check above: wait for their answers. Then create the notes file:

```markdown
## Never touch
- <each thing they named, as an exact path where possible, or "none">

## Cleanups
```

If they named a type rather than a folder ("all videos"), write it as they said it and apply it as a filter. If they add something later in any session, append it to `## Never touch` straight away and confirm in one line.

If they asked to see everything first, measure (step 2) and show the full numbered list of what you'd look at, grouped, so they can strike items before anything happens. Add anything they strike to the never-touch list only if they say "never"; otherwise skip it just for this session.

## 1. Start
1. **Quick or deep** (skip if already answered, or if the request names specific folders: then measure those, plus the overview if they turn out small).
2. **Free space**: check it with the guide's command and say it in plain words ("32 GB free out of 460 GB").
3. **Backup**: check with the guide's method and translate the answer: recent (fine), old (warn), none set up (warn clearly), couldn't check (ask when they last backed up). With no recent backup, safe caches are still fine. Personal items are the risk, so suggest backing up before any of those, and ask if they want to continue.

## 2. Measure
Use the guide's measuring command with its 2-minute limit, one folder per command, so a slow folder can't stall the rest. No output means it hit the limit: say you skipped it and move on. A size plus "permission denied" errors is a partial count: say "at least" that size. Skip anything under about 100 MB.

- **Quick clean**: only the SAFE locations in the guide.
- **Deep clean, or a narrow question that turned out small**: run the guide's overview list first to see where the space really is, then go one level deeper only in the big folders.
- **Big files**: use the guide's fast search, never a full crawl of the home folder.

Before calling an app's data "left behind", confirm the app is really gone and look at the date: data changed in the last month means the app is still in use. When unsure, it's CHECK.

Before clearing an app's cache, check whether the app is running. If it is, say so in the SAFE list itself ("Chrome cache 4.1 GB, quit Chrome first or I'll skip it") so the one "yes" stays one yes.

## 3. Sort and ask
Sort findings into three groups, biggest first:
- **SAFE**: caches, logs, temp files. Things that rebuild themselves.
- **CHECK WITH ME**: anything personal or unclear. One row per item with size and date. Mark important ones with ⚠️ (see below).
- **DO NOT TOUCH**: system files, the never-touch list, and the guide's do-not-touch list. Mention big ones so the user knows they were seen.

Show the full SAFE list as a table with sizes and a total, then wait for one "yes".

Then go through CHECK WITH ME one group at a time (Trash, then Downloads, then phone backups, and so on), each item with its own "yes". A wall of tables at once overwhelms people. Give a one-line preview of the groups ahead. In that preview, say once that personal items go to the Trash, so they can be put back.

## 4. Important items: pause and warn
Some things hurt to lose. When the user says yes to one of these, don't act on that first yes. Stop and give a short caution, then wait for a second, specific confirmation.

**Important means any of:**
- Photos, videos, music, documents, or anything that looks like their own work (projects, exports, drafts, source code that isn't a rebuildable build folder)
- Anything changed in the last 30 days
- Phone backups, especially the only or newest one
- Emptying the Trash or Recycle Bin (permanent)
- Chat app media, password or key files, database volumes (Docker volumes, local databases)
- Anything over 5 GB that isn't a cache
- Any personal item when there's no recent backup

**The caution, in this shape:**
> ⚠️ Before I do this: **[item]** ([size], last changed [date]).
> - What you'd lose: [plain words, e.g. "the only backup of your iPhone from March"].
> - Can it come back? [e.g. "Yes, from the Trash until you empty it" / "No, emptying the Trash is permanent"].
> - Backup: [e.g. "You don't have a backup, so this can't be recovered."]
>
> Type **"yes, [short item name]"** to go ahead, or "skip".

Say "permanent" or "can't be undone" once in the caution, not in every line around it.

A plain "yes" or "ok" after the caution is not enough for permanent actions (emptying the Trash, removing database volumes): ask for the item name. For reversible actions (moving to the Trash), a clear "yes" after the caution is fine. If they say skip, move on without comment. Never argue them out of it twice: one clear caution, then respect their call.

## 5. Delete safely
Use the guide's commands:
- **SAFE items** are deleted for good. Prefer a tool's own clean command (npm, pip, brew, docker and so on) when the tool is installed, because it knows its own layout. For plain cache folders, empty the folder but keep the folder itself.
- **CHECK WITH ME items** go to the Trash or Recycle Bin in a way that supports "Put Back" or "Restore", one exact path at a time.
- **Emptying the Trash** is always its own question, with the ⚠️ caution. Show the biggest items inside it with dates first: people throw things away by mistake.
- Files that refuse to delete because the system protects them: skip, don't escalate to admin rights. Files that refuse only because they're read-only and owned by the user may be made writable and removed.

After any move to the Trash, remind the user the space only comes back once the Trash is emptied.

## 6. Finish
Check free space again, then show:

| | |
|---|---|
| Free space before | ... |
| Free space after | ... |
| Cleared | each item with its size |
| Skipped (app open, or you said no) | ... |
| In the Trash, waiting for you | ... |

Then 2-3 tips picked for what you actually found (the guide has system-specific ones).

Append a 1-2 line entry under `## Cleanups` in the notes file: date, free before and after, what was cleared, what was skipped and why. Leave `## Never touch` intact.

## Never delete without asking
- Photos, videos, music, documents and exported projects
- Screenshots and screen recordings from the last 30 days
- Anything in Desktop, Documents, Downloads, Pictures, Movies/Videos or Music, unless the user said yes to that exact item

## Never touch, on any system
- Operating system folders, system snapshots and restore points, boot files
- Browser profiles (logins, passwords, history). Only the cache subfolders are fair game
- Cloud sync folders (iCloud Drive, OneDrive, Dropbox, Google Drive): deleting there deletes everywhere
- Photo libraries and messaging app folders (clear media from inside the app instead)
- Virtual machine and container disk images
- Password manager, SSH and GPG key folders
