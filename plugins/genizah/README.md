# Genizah

Genizah is a free skill that helps you free disk space on Mac, Windows and Linux without losing anything that matters. It measures first, sorts what it finds into Safe, Check with you and Do not touch, asks before anything personal, and sends personal files to the Trash or Recycle Bin instead of deleting them.

## What it runs

The skill tells Claude to use ordinary local commands to measure folders (such as `du` on Mac and Linux, PowerShell on Windows) and to move or remove files you approve. It deletes by exact path, never by wildcard, and never uses `sudo` or an elevated PowerShell without asking first and saying why.

## What it never touches

System files, cloud sync folders, photo libraries, password and SSH folders, and anything you name.

## What it sends

Nothing. Genizah has no hooks, no MCP servers, no scripts and makes no network calls. It is instructions only.

## Links

- Site: https://genizah.twistorlabs.com
- Source: https://github.com/Twistor-Labs/genizah
- Publisher: Twistor Labs Private Limited, hello@twistorlabs.com
- License: MIT
