# 🫏 Brick Progress · Slacking Edition

A playful floating desktop tool for "slacking off" at work. Semi-transparent glassy UI, Always-on-Top, collapsible and draggable — shrink it down when the boss comes by.

## ✨ Features

| Module | Description |
|--------|-------------|
| 🐟 Mermer Pet | Desktop pet with happiness meter + slack level + exp bar |
| 📊 Brick Progress | Custom daily goal · real-time coins · slack level |
| 🎮 Mini Games | Block puzzle · breakout · coin clicker · focus mode |
| 🐴 Donkey Grind | Leave it running to earn coins and exp passively |
| 🐾 Companion Dex | Collect different slack companions |
| ⏰ Memos & Schedule | Built-in reminders · optional calendar integration |
| 📈 Stock Ticker | Real-time A-shares / US / HK quotes |
| 🌤️ Weather | Compact weather panel in the corner |
| 💬 Slacking Quotes | Rolling motivational quotes |

### 🐟 Slack Level System (v2.0 new)

- **8-level progression**: Slack Intern → Apprentice → Specialist → Expert → Master → Grandmaster → Legend → Slack God
- **Difficulty curve**: exp needed per level scales as `300 × 1.65^lv` (300 → 495 → 817 → ... → 9989)
- **Happiness bonus**: better mood = faster exp, from ×0.35 up to ×1.6 across 6 tiers
- **Exp sources**: mini-game scores · feeding companions · sending them to slack · donkey grind · quick-click income · coin-earn ratio

## 📁 Layout

```
mermer-horse/
├── index.html                        # Main app · pure frontend · edit this to change everything
├── index.v1.bak                      # v1.0 backup
├── 一起来搬砖.exe                    # Electron portable build (double-click · 79 MB · recommended)
├── 搬砖进度条-WebView2版.exe         # WebView2 single-file build (1.2 MB · lightweight)
└── demo-donkey.html                  # Donkey grind standalone demo
```

## 🚀 How to run

Both `.exe` files are **portable · no install needed · double-click to launch**:

- **`一起来搬砖.exe`** (recommended) — Electron wrapper, transparent floating window, collapse/expand animation, multi-monitor aware
- **`搬砖进度条-WebView2版.exe`** (lightweight) — single 1.2 MB file, requires the WebView2 Runtime that comes with Win10+

> Older Windows without WebView2 (e.g. Win7) will complain — install Microsoft's official WebView2 Runtime once.

**Controls**:
- Drag anywhere empty → reposition
- Hover / click the ⊟ handle → expand; click again → collapse to a small icon
- `F` → focus mode (hide everything except a single pixel)
- `Space` → quick coin bonus (an Easter egg)

## 🔧 Rebuild after editing `index.html`

The HTML is embedded inside each `.exe`, so editing `index.html` alone won't update the shipped binaries. Two paths:

### Electron build (`一起来搬砖.exe`)

1. Extract the exe (7-Zip opens it directly), swap `index.html` inside `resources/app.asar`
2. Update the asar header JSON **and** the `ELECTRONASAR` integrity hash in `BrickProgress.exe` — Electron verifies this on start and refuses to run if mismatched
3. Repack

```bash
# Quick script (requires pefile + 7z + NSIS)
python3 tools/repack_electron.py
```

Or grab an NSIS silent-SFX template and repack the patched `app.asar` + `BrickProgress.exe`.

### WebView2 build (`搬砖进度条-WebView2版.exe`)

The HTML is embedded as a `RCDATA/202` PE resource — patch it directly with `pefile`:

```bash
python3 -c "
import pefile, struct
pe = pefile.PE('搬砖进度条-WebView2版.exe')
# Locate RCDATA 202, update Offset/Size, append new HTML to the file tail and backfill VA
"
```

No integrity check on the WebView2 build — patch and double-click.

## 💾 Data storage

Everything lives in browser `localStorage` under the `brick.*` namespace:

```
brick.cfg.enc.v1     # Encrypted config
brick.fun.v3         # Coins · pet state · companions
brick.reports.v1      # Daily reports
brick.memos.v1        # Memos
brick.logs.v1         # Logs
brick.marks.v1        # Slack marks
```

Electron version stores data under `%APPDATA%\BrickProgress`; WebView2 uses its own user-data dir. The two versions are **not interoperable** — copy localStorage files manually or reset each one separately.

## 🛠️ Tech stack

- Plain HTML / CSS / vanilla JS (no framework · ~3000 LOC)
- Electron wrapper for the floating window (transparent + Always-on-Top + frameless)
- WebView2 single-file fallback for lightweight deployment
- localStorage persistence + lightweight XOR encryption

## 📜 License

MIT — do whatever you want.
