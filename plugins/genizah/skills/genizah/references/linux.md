# Linux guide

## Basics
| Job | Command |
|---|---|
| Free space | `df -h /` (and `df -h /home` if it's a separate partition) |
| Measure, 2-minute limit | `timeout 120 du -sh "<path>" 2>/dev/null` |
| One level deeper | `du -h --max-depth=1 "<path>" 2>/dev/null` |
| Stay on one disk | add `-x` to `du` (skips network drives and other mounts) |
| Last changed | `stat -c '%y' "<path>" \| cut -d' ' -f1` |
| Big files | `timeout 120 find ~ -xdev -type f -size +1G 2>/dev/null` (home only, one disk) |
| Is an app running? | `pgrep -il "<name>"` |
| Which distro? | `. /etc/os-release; echo "$ID"` (picks apt, dnf, pacman, zypper) |

## Backup check
Look for a configured tool: `command -v timeshift deja-dup borg restic`. Timeshift's list needs `sudo`, so don't run it: ask instead. Déjà Dup: `gsettings get org.gnome.DejaDup last-backup` shows the last run on GNOME. Nothing found: ask the user, and treat "not sure" as no backup.

## Overview list (deep clean)
Measure one at a time: `~/.cache`, `~/.local/share`, `~/.config`, `~/.local/share/Trash`, `~/Downloads`, `~/Desktop`, `~/Documents`, `~/Videos`, `~/snap`, `~/.var/app` (Flatpak data), `~/.local/share/containers` (Podman).

## Locations
| Where | What it is | Group |
|---|---|---|
| `~/.cache` | App and tool caches | SAFE, except models and Python environments |
| `~/.cache/thumbnails` | Image previews | SAFE |
| `~/.npm`, `~/.cache/pip`, `~/.cache/uv`, `~/.cache/yarn`, `~/.gradle/caches`, `~/.cargo/registry` | Developer caches | SAFE (prefer the tool's command) |
| `~/.local/share/Trash` | Thrown-away files | CHECK ⚠️ to empty |
| `~/Downloads` | Installers (.deb, .rpm, .AppImage, archives) and big files | CHECK |
| `~/snap/<app>/` old revision folders | Old snap app data | CHECK |
| `~/.var/app/<app>` | Flatpak app data | CHECK (data from uninstalled apps only) |
| Unused Flatpak runtimes | Shared libraries no app needs | SAFE: `flatpak uninstall --unused --user` |
| Docker / Podman | Containers, images, volumes | CHECK (`docker system df -v`, `docker system prune`; volumes ⚠️) |
| Package cache, system journal, old kernels, old snap revisions | System-level | Admin needed (see below) |

## Admin-only cleanups
These need `sudo`. Never run one without asking, saying exactly what it does and roughly how much it frees:
| Job | Command (Debian/Ubuntu shown; adapt per distro) |
|---|---|
| Package download cache | `sudo apt clean` (dnf: `sudo dnf clean all`, pacman: `sudo paccache -r`) |
| Old system logs | `sudo journalctl --vacuum-time=2weeks` (check size first: `journalctl --disk-usage`, no sudo) |
| Unused packages and old kernels | `sudo apt autoremove` (show the list it prints and confirm; never remove the running kernel, `uname -r`) |
| Old snap revisions | `snap list --all \| awk '/disabled/{print $1, $3}'`, then `sudo snap remove <name> --revision=<rev>` one at a time |

## Traps
- **npm**: `npm cache clean --force` only clears `~/.npm/_cacache`. Measure that, not all of `~/.npm`: `_npx` holds tools in use and stays.
- **Model folders** (`~/.cache/huggingface`, `torch`, `whisper`, `~/.ollama`) and Python environments: CHECK, not SAFE.
- **Browser profiles** (`~/.mozilla/firefox/<profile>`, `~/.config/google-chrome`): never touch. Their caches live in `~/.cache/mozilla` and `~/.cache/google-chrome` and are SAFE with the browser closed.
- **Mounted drives** under `/media`, `/mnt`, `/run/media`: never measure or delete there unless asked; always use `du -x` and `find -xdev`.
- **Docker disk data** in `/var/lib/docker`: only through `docker` commands, never by hand.

## Delete commands
| Job | Command |
|---|---|
| Tool caches | `npm cache clean --force`, `pip cache purge`, `uv cache clean`, `yarn cache clean`, `cargo cache -a` (if installed) |
| Empty a cache folder, keep the folder | `find "<exact dir>" -mindepth 1 -maxdepth 1 -exec rm -rf {} +` |
| Personal item to Trash (supports Restore) | `gio trash "<exact path>"` (fallback: `kioclient5 move "<path>" trash:/` on KDE) |
| Restore an item from the Trash | `gio trash --restore "trash:///<item>"` (back to its original folder; `gio list trash:///` shows names) |
| Empty Trash (⚠️ permanent) | `gio trash --empty` |

## Do not touch
`/boot`, `/usr`, `/etc`, `/var/lib` (except through its tools), `/proc`, `/sys`, `/snap` directly, the running kernel, Timeshift snapshots, `~/.ssh`, `~/.gnupg`, `~/.local/share/keyrings`, cloud sync folders.

## Tips to pick from
- GNOME: Settings > Privacy > File History & Trash > turn on "Automatically delete trash content".
- Set up Timeshift or Déjà Dup with an external drive.
- Run `flatpak uninstall --unused` now and then.
- Limit journal size: `SystemMaxUse=500M` in `/etc/systemd/journald.conf` (admin).
