# 📱 iPhone Camera Roll → PC Backup via iOS Shortcuts

> **Copy photos and videos from your iPhone Camera Roll to a Windows PC folder — by date range — over Wi-Fi. No iTunes. No iCloud. No third-party apps.**

![iOS](https://img.shields.io/badge/iOS-18%2B%20%7C%20iOS%2026-black?logo=apple&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Windows%20PC-blue?logo=windows&logoColor=white)
![Method](https://img.shields.io/badge/Transfer-SMB%20over%20Wi--Fi-green)
![License](https://img.shields.io/badge/License-MIT-yellow)
![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-brightgreen)

---

## 🧩 The Problem

You have hundreds of photos and videos on your iPhone that you want to back up to a specific folder on your Windows PC — filtered by a date range. But:

- **iTunes** is bloated and doesn't let you pick a date range
- **iCloud** costs money and requires internet
- **AirDrop** is one file at a time
- **Third-party apps** require subscriptions or show ads
- **Windows Explorer via USB** shows everything with no filtering

This guide shows you how to build a **free, native iOS Shortcut** that:
1. Asks you for a **From Date** and **To Date**
2. Finds all matching **photos and videos**
3. Copies them directly to a **Windows shared folder** over Wi-Fi (SMB)

---

## ✨ Features

- ✅ **Date range filtering** — copy only what you need
- ✅ **Photos + Videos handled separately** — optimised 2-pass approach
- ✅ **No re-encoding** — videos use `Passthrough` mode (original quality preserved)
- ✅ **Deduplication** — overwrites existing files instead of creating duplicates
- ✅ **Silent operation** — no prompts mid-transfer
- ✅ **Completion notification** — know when it's done
- ✅ **iOS 26 Automation support** — schedule to run automatically every night
- ✅ **Apple Intelligence prompt included** — generate the shortcut with one sentence
- ✅ **Zero cost** — uses only built-in iOS Shortcuts app + Windows file sharing

---

## 📋 Compatibility

| Component | Requirement |
|-----------|-------------|
| iPhone iOS | iOS 18, iOS 18.x, iOS 26+ |
| Apple Intelligence | Optional (speeds up setup only) |
| PC OS | Windows 10 / 11 |
| Network | Both devices on the same Wi-Fi |
| Apps needed | iOS **Shortcuts** app (pre-installed) |

---

## 🗺️ How It Works

```
iPhone Shortcuts App
        │
        ▼
📅 Ask: From Date  ──┐
📅 Ask: To Date    ──┤
                     │
        ▼            │
🔍 Find Photos (Media Type = Photo, Date in range) ──► OnlyPhotos
🔍 Find Photos (Media Type = Video, Date in range) ──► OnlyVideos
                     │
        ▼            │
❓ If OnlyPhotos has any value
  │
  ├─► 🔁 Photo Loop ──► 💾 Save File (direct)
  │                          │
  │                          ▼
  │                    Windows PC Shared Folder
  │                    (via SMB over Wi-Fi)
  │
  ├─► 🔁 Video Loop ──► 🎬 Encode Media (Passthrough)
  │                          │
  │                          ▼
  │                     💾 Save File (Encoded Media)
  │                          │
  │                          ▼
  │                    Windows PC Shared Folder
  │
  └─► 🔔 Show Notification: Done!

  Otherwise ──► ⚠️ Alert: No files found → Stop
```

---

## 🚀 Quick Start

### Step 1 — Set up Windows file sharing
Enable a shared folder on your Windows PC and note your **PC's local IP address** (`ipconfig` in Command Prompt).

### Step 2 — Connect iPhone to PC share
On iPhone → **Files app** → `...` → **Connect to Server** → `smb://YOUR_PC_IP/ShareName`

### Step 3 — Build the Shortcut
Follow [GUIDE.md](./GUIDE.md) — full step-by-step with screenshots of every action.

### Step 4 — Run it
Tap the shortcut on your Home Screen → pick dates → done. ✅

---

## 📖 Full Guide

> 👉 See **[GUIDE.md](./GUIDE.md)** for the complete step-by-step instructions.

The guide covers:

| Section | What's inside |
|---------|--------------|
| **Part 1** | One-time SMB setup — connecting PC share to iPhone Files app |
| **Part 2 Option A** | Apple Intelligence prompt to auto-generate the shortcut |
| **Part 2 Option B** | Manual action-by-action build guide |
| **Action 1–2** | Date range inputs (`FromDate`, `ToDate`) |
| **Action 3A** | Find Photos filtered by `Media Type = Photo` |
| **Action 3B** | Find Photos filtered by `Media Type = Video` |
| **Action 4** | Guard clause — stop if nothing found |
| **Action 5** | Photo copy loop — direct Save File, no encoding |
| **Action 6** | Video copy loop — Encode Media (Passthrough) → Save File |
| **Action 7** | Completion notification with file count |
| **Part 3–4** | Naming, saving, and adding to Home Screen |
| **Troubleshooting** | 9 common errors with exact fixes |
| **Bonus** | iOS 26 Automation — runs every night automatically |

---

## ⚠️ Known Errors & Fixes

### ❌ `wfphotomediaContentItem produced no file representation for type com.apple.quicktime-movie`

**Cause:** You're passing a video's `Repeat Item` directly to `Save File`. iOS cannot materialise a raw Camera Roll video reference into a file without first processing it.

**Fix:** Add **`Encode Media`** (Size: `Passthrough`, Speed: `Normal`) before `Save File` in the video loop. Use `Encoded Media` as the file input — not `Repeat Item`.

---

### ❌ `If` action does not show an `is` condition

**Cause:** You are connecting `Repeat Item` directly as the `If` input. The `If` action cannot determine media type from a raw photo library item.

**Fix:** Use two separate **`Find Photos`** actions filtered by `Media Type = Photo` and `Media Type = Video` respectively (Actions 3A and 3B). This eliminates the need for any `If` check inside the loop entirely.

---

## 🛠️ Troubleshooting

| Problem | Fix |
|---------|-----|
| `wfphotomediaContentItem` error on videos | Use **Encode Media (Passthrough)** before Save File in the video loop |
| `If` action has no `is` condition | Filter media type in **Find Photos** (3A/3B), not with If inside the loop |
| Find Photos returns 0 results | Ensure date values are **variables** (`FromDate`/`ToDate`), not typed text |
| Save File fails or errors | Reconnect SMB in Files app, then re-select folder in the shortcut |
| Shortcut freezes mid-transfer | Screen auto-locked — set **Auto-Lock → Never** before running |
| Files saved to wrong folder | Edit shortcut → Save File → re-select correct SMB network folder |
| SMB share not in Files picker | Open Files app → Connect to Server first |
| Transfer is very slow | Normal for SMB over Wi-Fi. Videos also need encode step |
| Can photos & videos copy in parallel? | ❌ iOS Shortcuts is single-threaded. The 2-pass approach is the best optimisation available |

---

## ⚙️ Before Running — Checklist

| | Check |
|-|-------|
| 📶 | Phone and PC are on the **same Wi-Fi network** |
| 💡 | Auto-Lock set to **Never** (Settings → Display & Brightness) |
| 🔓 | PC folder sharing is **active and accessible** |
| 📁 | SMB share **visible in Files app** under Network |
| 🔋 | Phone is **plugged in** or fully charged |

---

## 💡 iOS 26 Bonus — Fully Automatic Nightly Backup

Schedule the shortcut to run every night automatically:

1. Shortcuts app → **Automation** tab → `+` → **New Automation**
2. Trigger: **Time of Day** → `2:00 AM`
3. Toggle: **Run Immediately** + **Ask Before Running → OFF**
4. Action: **Run Shortcut** → `📷 Copy Photos to PC`
5. Set date inputs to **"Yesterday"** for incremental daily backup

Your photos and videos back up automatically while you sleep. 🌙

---

## 📁 Repository Structure

```
iphone-camera-roll-to-pc/
│
├── README.md          ← You are here — project overview
├── GUIDE.md           ← Full step-by-step shortcut build guide
├── TROUBLESHOOTING.md ← Standalone error reference
└── LICENSE            ← MIT License
```

---

## 🤝 Contributing

Contributions are welcome! If you find a new error, a better action sequence, or want to add support for Mac as the destination — open a PR or file an issue.

1. Fork the repo
2. Create your branch: `git checkout -b fix/your-fix-name`
3. Commit your changes: `git commit -m 'fix: describe what you fixed'`
4. Push: `git push origin fix/your-fix-name`
5. Open a Pull Request

---

## 📌 Topics / Tags

`ios-shortcuts` · `iphone-backup` · `smb` · `windows` · `photo-transfer` · `ios26` · `apple-shortcuts` · `camera-roll` · `no-itunes` · `wifi-transfer` · `automation`

---

## 📄 License

MIT License — free to use, share, and modify.

---

## ⭐ If this helped you

Give the repo a **star** ⭐ — it helps others find it too.

> Built from real troubleshooting. The `wfphotomediaContentItem` error took hours to figure out — now it'll take you 30 seconds.
