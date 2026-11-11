# Windows guide

Use PowerShell. Paths below use environment variables; expand them to exact paths before deleting.

## Basics
| Job | Command |
|---|---|
| Free space | `Get-PSDrive C \| Select-Object Used,Free` (say it in GB) |
| Measure, 2-minute limit | `$j = Start-Job { (Get-ChildItem -LiteralPath $using:p -Recurse -Force -File -ErrorAction SilentlyContinue \| Measure-Object Length -Sum).Sum }; if (Wait-Job $j -Timeout 120) { Receive-Job $j } else { Stop-Job $j; 'skipped' }` (set `$p` first) |
| One level deeper | Run the measure command on each `Get-ChildItem -LiteralPath $p -Directory -Force` |
| Last changed | `(Get-Item -LiteralPath $p -Force).LastWriteTime.ToString('yyyy-MM-dd')` |
| Big files | `Get-ChildItem $env:USERPROFILE -Recurse -File -Force -ErrorAction SilentlyContinue \| Where-Object Length -gt 1GB` (slow: run as a job with the 2-minute limit, or use Windows Search if indexed) |
| Is an app running? | `Get-Process -Name chrome -ErrorAction SilentlyContinue` |
| Is an app installed? | `Get-StartApps \| Where-Object Name -like '*<name>*'` and `Get-AppxPackage *<name>*` |
| Admin? | `([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole('Administrators')` |

## Backup check
Windows has no reliable command for this without admin rights. Check whether OneDrive folder backup is on (`Test-Path "$env:OneDrive"`) and ask the user: "Do you back up with Windows Backup, File History, OneDrive or an external drive? When did it last run?" Treat "not sure" as no backup.

## Overview list (deep clean)
Measure one at a time: `$env:LOCALAPPDATA`, `$env:APPDATA`, `$env:TEMP`, `$env:USERPROFILE\Downloads`, `$env:USERPROFILE\Desktop`, `$env:USERPROFILE\Documents`, `$env:USERPROFILE\Videos`, `$env:LOCALAPPDATA\Packages`, the Recycle Bin (`C:\$Recycle.Bin` is per-user and protected: estimate from Explorer or skip).

## Locations
| Where | What it is | Group |
|---|---|---|
| `$env:TEMP` | Temp files | SAFE (in-use files will refuse, skip them) |
| `$env:LOCALAPPDATA\Microsoft\Windows\INetCache` | Edge/IE web cache | SAFE |
| `$env:LOCALAPPDATA\Google\Chrome\User Data\<Profile>\Cache` and `Code Cache` | Chrome cache (only these subfolders) | SAFE with Chrome closed |
| `$env:LOCALAPPDATA\Microsoft\Edge\User Data\<Profile>\Cache` | Edge cache | SAFE with Edge closed |
| `$env:LOCALAPPDATA\npm-cache`, `$env:LOCALAPPDATA\pip\Cache`, `$env:LOCALAPPDATA\Yarn\Cache`, `$env:USERPROFILE\.gradle\caches`, `$env:USERPROFILE\.nuget\packages` | Developer caches | SAFE (prefer the tool's command) |
| `$env:LOCALAPPDATA\CrashDumps`, `$env:LOCALAPPDATA\Microsoft\Windows\WER` | Crash reports | SAFE |
| Recycle Bin | Thrown-away files | CHECK ⚠️ to empty |
| `$env:USERPROFILE\Downloads` | Installers (.exe, .msi, .zip) and big files | CHECK |
| `$env:APPDATA\Apple Computer\MobileSync\Backup`, `$env:USERPROFILE\Apple\MobileSync\Backup` | iPhone backups | CHECK ⚠️ |
| `$env:LOCALAPPDATA\Packages\5319275A.WhatsAppDesktop_*` | WhatsApp media | CHECK, clear inside the app |
| `$env:LOCALAPPDATA\Android\Sdk` | Android SDK and emulators | CHECK |
| Docker Desktop | Containers, images, volumes | CHECK (`docker system df -v`, `docker system prune`; volumes ⚠️) |
| `C:\Windows.old` | Previous Windows version (rollback) | CHECK ⚠️, admin needed: point to Settings > System > Storage > Temporary files |
| `C:\Windows\SoftwareDistribution\Download`, Delivery Optimization files | Old update downloads | Admin needed: point to Settings > System > Storage > Temporary files |

## Traps
- **Admin**: anything under `C:\Windows` or `C:\Program Files` needs an elevated PowerShell. Don't do it. Send the user to **Settings > System > Storage > Temporary files**, which cleans those safely through Windows itself.
- **Long or locked paths**: use `-LiteralPath` everywhere. In-use files fail with "being used by another process": skip, don't force.
- **Chrome and Edge `User Data`** is the browser profile (passwords, history). Only `Cache` and `Code Cache` inside a profile are fair game.
- **WSL and Docker disk images** (`ext4.vhdx`, `docker_data.vhdx`) never get deleted. Shrinking them is an advanced job: mention, don't do.
- **OneDrive folders**: deleting there deletes in the cloud and on every device. Files marked "online-only" take no local space anyway.

## Delete commands
| Job | Command |
|---|---|
| Tool caches | `npm cache clean --force`, `pip cache purge`, `yarn cache clean`, `dotnet nuget locals all --clear` |
| Empty a cache folder, keep the folder | `Get-ChildItem -LiteralPath $p -Force \| Remove-Item -Recurse -Force -ErrorAction SilentlyContinue` |
| Personal file to Recycle Bin (supports Restore) | `Add-Type -AssemblyName Microsoft.VisualBasic; [Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile($p,'OnlyErrorDialogs','SendToRecycleBin')` |
| Personal folder to Recycle Bin | same, with `DeleteDirectory($p,'OnlyErrorDialogs','SendToRecycleBin')` |
| Restore an item from the Recycle Bin | `$sh = New-Object -ComObject Shell.Application; $sh.Namespace(10).Items() | Where-Object Name -eq "<item>" | ForEach-Object { $_.InvokeVerb("undelete") }` (goes back to its original folder) |
| Empty Recycle Bin (⚠️ permanent) | `Clear-RecycleBin -DriveLetter C -Force` |

## Do not touch
`C:\Windows` (including `WinSxS`), `C:\Program Files`, `C:\ProgramData`, `pagefile.sys`, `hiberfil.sys`, `swapfile.sys`, System Restore points, `C:\$Recycle.Bin` directly, `$env:LOCALAPPDATA\Microsoft\Credentials`, `$env:APPDATA\Microsoft\Protect`, `$env:USERPROFILE\.ssh`, OneDrive folders.

## Tips to pick from
- Turn on **Storage Sense** (Settings > System > Storage) to clear temp files and the Recycle Bin automatically.
- Use OneDrive "Free up space" to make old files online-only.
- Clear WhatsApp media: WhatsApp > Settings > Storage.
- Set up Windows Backup or File History with an external drive.
