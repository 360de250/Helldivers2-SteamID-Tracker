# Helldivers2 SteamID-Tracker

# kick — SteamID Tracker & Blacklist Checker

**Version:** kickv19b
**Dependency:** Bingus Shared Loader (loader v15+ / API 1)
**Hotkey:** `]`
**Type:** Client-side local mod, only affects your own screen
Feel free to unpack, modify, and improve it to make a better mod. There probably won't be any more updates, and I can't push updates for bugs I didn't anticipate.
---

## What does this mod do?

Once you're in a mission, press `]` once. It scans the SteamIDs of all players in the current match and compares them against your blacklist.

- **Blacklist hit**: A red warning pops up in the center of your screen showing how many people matched, which blacklist file it came from, and their Steam profile links.
- **No hit**: Nothing shows up, but it writes the SteamIDs of all players in the match to a file so you can check later.

**It won't kick anyone, won't automate anything, won't modify game behavior.** It's just an "identifier."

---

| Step | Action |
|---|---|
| 1 | You can scan on the ship or in a mission, as long as someone's there. It doesn't exclude yourself (important: it only scans players currently in the match) |
| 2 | Press `]` |
| 3 | Wait **8–15 seconds** (full memory scan, don't press it a second time mid-scan, and don't leave the mission. The AI said this, but just use your own judgment—it takes time. Anyway, don't spam it) |
| 4 | If there's a hit, a red warning pops up in the center for 4 seconds (if you can't be bothered to look, just hit F12 to screenshot. It's mainly a heads-up for clients when you happen to scan and run into one) |

**You can play normally during the scan, but there'll be a slight stutter** (a few milliseconds per second of overhead—barely noticeable on my old laptop from college days). It goes back to normal once done.

---

## What you see on screen

When there's a hit, the center of your screen shows from top to bottom:

```
[!] BLACKLIST: 1
source kick_blacklist.txt:24
https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX
show 4s, F12 screenshot
```

| Line | Meaning |
|---|---|
| `[!] BLACKLIST: N` | N people matched in this match |
| `source filename:line` | Which line in which blacklist file it matched |
| URL | Their Steam profile—**click it to see who they are** |
| `show 4s, F12 screenshot` | Note: disappears after 4 seconds, press F12 to save proof |

**`F12` is Steam's built-in screenshot key.** Note: the screenshot will **include this warning box**—that's exactly what we want, so you have proof later.

If you don't screenshot within 4 seconds, it's gone, but `blacklist_hits.txt` keeps a permanent record. See below.

---

## How to maintain your blacklist

All files are in:

```
%LOCALAPPDATA%\CowboyBingus\Helldivers2\Logs\
```

Copy the line above, paste it into your file explorer's address bar, and hit Enter.

### Two blacklist files

| File | Purpose |
|---|---|
| `kick_blacklist.txt` | **Your own** blacklist |
| `kick_blacklist_imported.txt` | **Someone else's** blacklist shared with you (kept separate to distinguish sources) |

**Both files use the exact same format.** When there's a hit, the log records which file and which line it matched.

### Format per line (pick one)

**Format A: Full three columns** (copy directly from the `player_*.txt` this mod generates—easiest)

```
39XXXXXXXXXXXXXX  01XXXXXXXXXXXXXX  https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX
```

**Format B: Just the Steam profile**

```
https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXXX
```

Or just the **17-digit number**:

```
76XXXXXXXXXXXXXXX
```

Both get normalized automatically—**write it however you want**.

### Rules

- `#` starts a comment. You can write notes about what they did, like:
  ```
  39XXXXXXXXXXXXXX  01XXXXXXXXXXXXXX  https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX  # 2026-09-15 suspected aimbot
  ```
- Blank lines are ignored
- **Case-insensitive** (compared in uppercase)
- Any of the three IDs (peer_id / steam64_hex / steam64_url) **matching counts as a hit**. A wrong column won't drag down the others
- Malformed lines get logged to `blacklist_hits.txt` (`# error @ ...`) so you can troubleshoot

### Do I need to restart the game after editing?

**No.** Both blacklist files are re-read every time you press `]` to scan. Just save and it takes effect on the next scan.

---

## Files generated per scan

| File | Content |
|---|---|
| `player_MMDD_HHMMSS.txt` | Three-column info for all players in the match (peer_id / steam64 / URL) |
| `blacklist_hits.txt` | **Append mode**, all historical hit records, never overwritten |
| `Kick.log` | Technical log for troubleshooting, don't normally need to look at it |

**How to use `player_*.txt`**: Open it, find the problematic player's line, **copy the whole line**, paste it into `kick_blacklist.txt`, add `# your note` at the end, save. Next time you run into them, you'll get an alert.

Anyway, besides kick_blacklist.txt and kick_blacklist_imported.txt, each press generates a player text file with a corresponding timestamp. You can delete them whenever—the more you use it, the more pile up. As for kick_blacklist.txt and kick_blacklist_imported.txt, if you delete them after using the mod, I don't know if it'll auto-generate default files again like the first time. Just thought of this, too lazy to test it. If not, just create new ones with the same names.

## FAQ


**Q: Scan takes forever?**
A: 8–15 seconds is normal. That's the cost of a full memory scan. It stops automatically when done.

**Q: HUD shows for only 4 seconds, too short to read?**
A: Doesn't affect the record. All hits are written to `blacklist_hits.txt`. Check it after the game.

**Q: URL starting with `7656119` won't open / 404?**
A: Probably a steam64 number copied from elsewhere that's **missing or has an extra digit**. Cross-check against the original URL in `player_*.txt`.

**Q: Will it affect teammates?**
A: **No.** This is a purely local mod, only runs on your own client. Other players can't see your blacklist, can't see your warning box, and can't tell you're scanning.

**Q: Will I get banned by anti-cheat?**
A: We're in the era of big mods now. I threw this mod together just to keep up with the times. This mod **doesn't read memory outside the game process, doesn't modify game code segments, doesn't write to any game memory**. It only reads its own process memory in-game for string matching, and only writes local log files. Whether to use it is your own call and your own risk.

**Q: Can it auto-kick?**
A: No. This is an identification tool. You still have to do the kicking yourself.

---

## Uninstall

Disable or delete `kick` in HD2 Mod Manager, then Deploy. Generated log files won't be auto-cleared. You can manually delete whatever you don't need under `%LOCALAPPDATA%\CowboyBingus\Helldivers2\Logs\`.

---

## Credits & Disclaimer
Thanks to all the open-source projects and mods that gave me experience, and thanks to DS.
This mod is for **self-protection**: identifying, recording, and sharing known problem players. Don't use it for harassment, revenge, or anything that violates Steam's Terms of Service. All data stays local. Whether to share it is up to you.
