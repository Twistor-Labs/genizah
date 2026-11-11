# macOS guide

## Basics
| Job | Command |
|---|---|
| Free space | `df -h /` (Finder may show more: it counts "purgeable" space macOS frees on its own) |
| Measure, 2-minute limit (`timeout` isn't on a stock Mac, `perl` is) | `perl -e 'alarm 120; exec @ARGV' du -sh "<path>" 2>/dev/null` |
| One level deeper | `du -h -d 1 "<path>"` (macOS `du` won't take `-s` and `-d` together) |
| Last changed | `stat -f '%Sm' -t '%Y-%m-%d' "<path>"` |
| Big files (Spotlight, instant) | `mdfind -onlyin ~ 'kMDItemFSSize > 1000000000'` |
| Is an app running? | `pgrep -il "<App Name>"` |
| Is an app installed anywhere? | `mdfind "kMDItemKind == 'Application'" \| grep -i "<name>"` |
| Same-name folders | `mdfind -onlyin ~ "kMDItemFSName == 'Downloads'"` |

## Backup check
Run `tmutil destinationinfo` and `tmutil latestbackup`. "No destinations configured" means no backup is set up. "Failed to mount destination" with a destination listed means the drive isn't plugged in. A permission error means you couldn't check: ask.

## Overview list (deep clean)
Measure one at a time: `~/Library/Group Containers`, `~/Library/Containers`, `~/Library/Application Support`, `~/Library/Caches`, `~/Library/Developer` (use `du -sh -I DeviceFS`), `~/.Trash`, `~/Desktop`, `~/Documents`, `~/Downloads`, `~/Movies`, `~/.cache`.

**Never count** `~/Library/Developer/CoreDevice/DeviceFS`: it's a window onto a connected iPhone and takes no space here, though `du` shows it as huge.

## Locations
| Where | What it is | Group |
|---|---|---|
| `~/Library/Caches` | App caches | SAFE (skip open apps) |
| `~/Library/Logs` | Old logs | SAFE |
| `~/Library/Containers/com.apple.mail/Data/Library/Mail Downloads` | Opened attachments. Originals stay in the email | SAFE |
| `~/.npm`, `~/Library/pnpm/store`, `~/.cache/uv`, `~/.bun/install/cache`, pip, yarn, Homebrew | Developer download caches | SAFE |
| `~/.cache` | Mixed tool caches | SAFE, except models and Python environments |
| `~/Library/Developer/Xcode/DerivedData` | Xcode build files | SAFE |
| `~/.Trash` | Thrown-away files | CHECK ⚠️ to empty |
| `~/Downloads` | Installers and big files | CHECK |
| `~/Library/Application Support/MobileSync/Backup` | iPhone/iPad backups | CHECK ⚠️ |
| Backups stored elsewhere (`mdfind -onlyin ~ "kMDItemFSName == 'Manifest.db'"`; each hit's folder is one backup) | iPhone/iPad backups moved by the user | CHECK ⚠️ |
| `~/Library/Group Containers` | Chat app media (WhatsApp, Telegram) | CHECK, clear inside the app |
| `~/Library/Application Support`, `~/Library/Containers` | Data from deleted apps | CHECK |
| `~/Library/Developer/Xcode/iOS DeviceSupport` | Debug files per iOS version | CHECK (old versions only) |
| `~/Library/Developer/CoreSimulator` | iPhone simulators | CHECK (`xcrun simctl delete unavailable`) |
| `~/Library/Android/sdk` | Android SDK and emulators | CHECK |
| Docker / OrbStack | Containers, images, volumes | CHECK (see below) |

## Traps
- **npm**: `npm cache clean --force` only clears `~/.npm/_cacache`. Measure that, not all of `~/.npm`: `_npx` holds tools in use and stays.
- **Model folders** (`~/.cache/huggingface`, `torch`, `whisper`, `~/.ollama`) are many GB and slow to re-download: CHECK. Python environments (`venv`, `.venv`, `*-venv`) don't rebuild on their own: CHECK.
- **"Operation not permitted"** on Containers, Mail or Trash: Terminal lacks Full Disk Access. Offer the fix (System Settings > Privacy & Security > Full Disk Access, turn on the terminal app, restart it) or skip.
- **Read-only cache files** owned by the user (some tools ship read-only `.app` bundles in caches): `chmod -R u+w "<exact path>"`, then remove. Never with `sudo`.
- **Docker / OrbStack**: never touch `Docker.raw` or `~/.orbstack`. Show `docker system df -v` (gives "created" dates). `docker system prune` for stopped containers, unused images and build cache. Unused volumes can hold databases: `docker volume prune` only with the ⚠️ caution.
- **Chrome profile** is `~/Library/Application Support/Google/Chrome`: never touch. Only `~/Library/Caches/Google/Chrome` is cache.
- **Final Cut Pro**: don't touch libraries. Point to File > Delete Generated Library Files.
- **CapCut, Premiere, DaVinci**: caches (CapCut cache, Premiere Media Cache, DaVinci CacheClip) are SAFE; projects, drafts and exports are not.
- **Claude desktop app**: `vm_bundles` are SAFE, they download again.

## Delete commands
| Job | Command |
|---|---|
| Tool caches | `npm cache clean --force`, `pnpm store prune`, `uv cache clean`, `bun pm cache rm`, `pip3 cache purge`, `yarn cache clean`, `brew cleanup` |
| Empty a cache folder, keep the folder | `find "<exact dir>" -mindepth 1 -maxdepth 1 -exec rm -rf {} +` |
| Personal item to Trash (supports Put Back, never overwrites) | `osascript -e 'tell application "Finder" to delete POSIX file "<exact path>"'` |
| Take something back out of the Trash before emptying | `mv -n "$HOME/.Trash/<item>" "$HOME/Desktop/"` (`-n` never overwrites; tell the user where it went) |
| Empty Trash (⚠️ permanent) | `osascript -e 'tell application "Finder" to empty trash'` |

## Do not touch
`/System`, `/private/var/db`, the Preboot volume, Time Machine local snapshots, `~/Library/Mobile Documents` (iCloud Drive), `~/Pictures/Photos Library.photoslibrary`, `~/Library/Messages`, `~/Library/Keychains`, `~/.ssh`, `~/.gnupg`.

## Tips to pick from
- Turn on **Empty Trash automatically** (System Settings > General > Storage).
- Clear WhatsApp media every few months: WhatsApp > Settings > Storage and Data > Manage Storage.
- Set up Time Machine with any external drive (System Settings > General > Time Machine).
- Move old videos to an external drive.
