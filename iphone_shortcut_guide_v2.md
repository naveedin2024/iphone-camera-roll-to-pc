# 📱 iPhone Shortcut: Copy Camera Roll by Date Range to PC
### For iOS 26 (and iOS 18+) — v2 with Optimised 2-Pass Copy

---

## ✅ PART 1 — One-Time Setup: Connect Your PC Share to iPhone Files App

> Do this once. After this, your PC folder will always appear in the Files app.

1. On your iPhone, open the **Files** app (blue folder icon)
2. Tap **Browse** (bottom tab)
3. Tap the **`...`** (three dots) button at the top right
4. Tap **"Connect to Server"**
5. Type your PC's address:
   ```
   smb://192.168.1.XXX/YourShareName
   ```
   *(Replace with your PC's actual local IP and share folder name)*
6. Tap **Connect**
7. Choose **"Registered User"** → enter your Windows username & password → tap **Next**
8. ✅ Your PC share now appears under **"Locations → Network"** in Files

> 💡 **To find your PC IP**: Open Command Prompt on PC → type `ipconfig` → use the **IPv4 Address**

---

## ✅ PART 2 — Create the Shortcut

### 🚀 OPTION A — Using Apple Intelligence (iOS 26, Fastest Method)

> If your device has **Apple Intelligence** enabled, you can describe what you want in plain English and it builds the shortcut for you.

1. Open the **Shortcuts** app
2. Tap **`+`** to create a new shortcut
3. Tap the **sparkle / AI icon** (✨) at the top or look for **"Describe your shortcut"** input
4. Type exactly:
   ```
   Ask me for a from date and a to date. Find all photos in my Camera Roll taken between
   those two dates and save each to my PC share folder on the network. Then find all videos
   in my Camera Roll taken between those two dates, encode each one, and save to the same
   PC share folder. Show me how many files were copied when done.
   ```
5. Tap **Generate** — Apple Intelligence will build most of the shortcut for you
6. **Review each action it created** — then follow Part 3 below to fix/configure the Save File destinations

---

### ⚙️ OPTION B — Manual Step-by-Step (Works on All iOS 18+ Versions)

#### Open Shortcuts

1. Open the **Shortcuts** app
2. Tap **`+`** (top right) → New Shortcut
3. Tap **"Add Action"** or use the **search bar at the bottom**

---

### 🔵 ACTION 1 — Ask for "From Date"

1. In the search bar type: **`Ask for Input`** → tap it
2. Tap **"Text"** (the Input Type field) → change to **"Date"**
3. Tap the **"Prompt"** field → type:
   ```
   From Date (copy photos starting from)
   ```
4. Tap on the result token/label at the top of the action block (it says "Input")
   → Rename it to **`FromDate`**

---

### 🔵 ACTION 2 — Ask for "To Date"

1. Tap **`+`** below the first action
2. Search **`Ask for Input`** → tap it
3. Change Input Type to **"Date"**
4. Set Prompt to:
   ```
   To Date (copy photos up to and including)
   ```
5. Rename the result variable to **`ToDate`**

---

### 🔵 ACTION 3A — Find Only **Photos** Between Those Dates

1. Tap **`+`** → search **`Find Photos`** → tap it
2. Inside the action, tap **"Add Filter"**:
   - **Field**: `Media Type`
   - **Condition**: `is`
   - **Value**: `Photo`
3. Tap **"Add Filter"** again:
   - **Field**: `Date Taken`
   - **Condition**: `is after`
   - **Value**: tap the value box → tap **"Variable"** → select **`FromDate`**
4. Tap **"Add Filter"** again:
   - **Field**: `Date Taken`
   - **Condition**: `is before`
   - **Value**: Select **`ToDate`**
5. Make sure the connector between all filters says **"All"** (= AND logic)
6. **Sort by**: Date Taken → Oldest First *(optional)*
7. **Limit**: leave toggled **OFF**
8. Tap the output variable label → rename it to **`OnlyPhotos`**

---

### 🔵 ACTION 3B — Find Only **Videos** Between Those Dates

1. Tap **`+`** → search **`Find Photos`** → tap it *(add a second one)*
2. Inside the action, tap **"Add Filter"**:
   - **Field**: `Media Type`
   - **Condition**: `is`
   - **Value**: `Video`
3. Tap **"Add Filter"** again:
   - **Field**: `Date Taken`
   - **Condition**: `is after`
   - **Value**: **`FromDate`**
4. Tap **"Add Filter"** again:
   - **Field**: `Date Taken`
   - **Condition**: `is before`
   - **Value**: **`ToDate`**
5. Make sure connector says **"All"**
6. **Sort by**: Date Taken → Oldest First *(optional)*
7. **Limit**: leave toggled **OFF**
8. Tap the output variable label → rename it to **`OnlyVideos`**

---

### 🔵 ACTION 4 — Stop if Nothing Found (Recommended)

1. Tap **`+`** → search **`If`** → tap it
2. Tap the input field → select **`OnlyPhotos`**
3. Set condition to **"has any value"**
4. Inside the **"Otherwise"** section:
   - Add **"Show Alert"** → message: `No photos or videos found in that date range. Exiting.`
   - Add **"Stop This Shortcut"**
5. Place all remaining actions inside the **"If"** (true) section

> 💡 You can add a second check for `OnlyVideos` too, but it is optional — if there are no videos the loop simply won't run.

---

### 🔵 ACTION 5 — Loop: Copy Photos (Fast — No Encoding Needed)

> Photos (JPEG, HEIC, PNG) are copied directly — no conversion required.

1. Inside the **"If"** block, tap **`+`**
2. Search **`Repeat with Each Item`** → tap it
   > *(In iOS 26 this is labelled "Repeat with Each Item")*
3. Tap the input of the Repeat action → select **`OnlyPhotos`**
4. Inside the loop, tap **`+`** → search **`Save File`** → tap it

   Configure **Save File**:

   | Field | Value |
   |-------|-------|
   | File | `Repeat Item` |
   | Destination | Browse → Network → Your PC share → target folder |
   | Ask Where to Save | **OFF** |
   | Overwrite if File Exists | **ON** |

---

#### 📐 Photo Loop Structure

```
🔁 Repeat with Each Item in OnlyPhotos
  │
  └─ 💾 Save File
         File:      Repeat Item
         Dest:      [PC SMB folder]
         Ask Where: OFF
         Overwrite: ON
End Repeat
```

---

### 🔵 ACTION 6 — Loop: Copy Videos (Encode First to Avoid QuickTime Error)

> Videos (`.MOV`) must be passed through **Encode Media** before saving.
> Saving `Repeat Item` directly causes:
> `wfphotomediaContentItem produced no file representation for type com.apple.quicktime-movie`

1. After the photo Repeat block ends, tap **`+`**
2. Search **`Repeat with Each Item`** → tap it
3. Tap the input → select **`OnlyVideos`**

#### Inside the Video Loop — Step A: Encode Media

4. Inside the loop, tap **`+`** → search **`Encode Media`** → tap it

   Configure **Encode Media**:

   | Field | Value |
   |-------|-------|
   | Media | `Repeat Item` |
   | Size | `Passthrough` *(preserves original quality — no re-encoding)* |
   | Speed | `Normal` |

#### Inside the Video Loop — Step B: Save File

5. Tap **`+`** → search **`Save File`** → tap it

   Configure **Save File**:

   | Field | Value |
   |-------|-------|
   | File | `Encoded Media` *(output of Encode Media above — NOT Repeat Item)* |
   | Destination | Browse → Network → Your PC share → target folder |
   | Ask Where to Save | **OFF** |
   | Overwrite if File Exists | **ON** |

---

#### 📐 Video Loop Structure

```
🔁 Repeat with Each Item in OnlyVideos
  │
  ├─ 🎬 Encode Media
  │      Media:  Repeat Item
  │      Size:   Passthrough
  │      Speed:  Normal
  │
  └─ 💾 Save File
         File:      Encoded Media
         Dest:      [PC SMB folder]
         Ask Where: OFF
         Overwrite: ON
End Repeat
```

---

### 🔵 ACTION 7 — Show Completion Summary (After Both Loops)

1. After the video Repeat block ends (outside both loops), tap **`+`**
2. Search **"Show Notification"** → tap it
3. Set the body to:
   ```
   ✅ Done! Photos and videos copied to your PC.
   ```
   > Optionally tap into the message body → tap **Variable** → insert **`OnlyPhotos.count`** and **`OnlyVideos.count`** for a file count summary

---

## 📐 Full Shortcut Structure at a Glance

```
📅 Ask for Input → FromDate
📅 Ask for Input → ToDate

🔍 Find Photos  (Media Type = Photo, Date between FromDate–ToDate) → OnlyPhotos
🔍 Find Photos  (Media Type = Video, Date between FromDate–ToDate) → OnlyVideos

❓ If OnlyPhotos has any value
  │
  ├─ 🔁 Repeat with Each Item in OnlyPhotos
  │     └─ 💾 Save File (Repeat Item → PC SMB folder)
  │    End Repeat
  │
  ├─ 🔁 Repeat with Each Item in OnlyVideos
  │     ├─ 🎬 Encode Media (Repeat Item, Passthrough)
  │     └─ 💾 Save File (Encoded Media → PC SMB folder)
  │    End Repeat
  │
  └─ 🔔 Show Notification "✅ Done!"

  Otherwise
  └─ ⚠️ Show Alert "No files found." → Stop This Shortcut

End If
```

---

## ✅ PART 3 — Name & Save Your Shortcut

1. Tap the shortcut **title** at the top (shows "New Shortcut" by default)
2. Tap **"Rename"** → type:
   ```
   📷 Copy Photos to PC
   ```
3. Tap the **icon button** next to the name to pick a custom color/emoji icon
4. Tap **"Done"** (top right)

---

## ✅ PART 4 — Add to Home Screen for Quick Access

1. In the Shortcuts app, find your new shortcut
2. Long-press on its card → tap **"Details"**
   *(or tap the `...` three-dot menu on the shortcut card)*
3. Tap **"Add to Home Screen"**
4. Give it a name and icon if desired → tap **"Add"**

Now you have a home screen button! 🎉

---

## ▶️ How to Run the Shortcut

1. Tap **📷 Copy Photos to PC** on your home screen
2. 📅 Date picker appears → select your **From Date** → tap **Done**
3. 📅 Another date picker → select your **To Date** → tap **Done**
4. The shortcut copies all **photos first** (fast), then **videos** (encode + copy)
5. 🔔 A notification appears when complete

---

## ⚠️ Before Running — Checklist

| Check | Action Required |
|---|---|
| 📶 Same Wi-Fi network | Phone and PC must be on the same local network |
| 💡 Screen stay-on | Settings → Display & Brightness → Auto-Lock → **Never** (reset after transfer) |
| 🔓 PC share is online | Ensure Windows folder sharing is active on your PC |
| 📁 SMB still connected | Open Files app and verify your PC share appears under Network |
| 🔋 Battery level | Large transfers take time — keep your phone plugged in or charged |

---

## 🛠️ Troubleshooting

| Problem | Fix |
|---|---|
| `wfphotomediaContentItem produced no file representation for type com.apple.quicktime-movie` | You are using `Repeat Item` directly in Save File for a video. Use **Encode Media (Passthrough)** first, then save `Encoded Media` — see ACTION 6 above |
| `If` action shows no `is` condition on `Repeat Item` | You cannot check media type directly on `Repeat Item` in an If action. Instead, filter by Media Type inside **Find Photos** (ACTION 3A/3B) — the 2-pass approach eliminates this problem entirely |
| "Find Photos" returns 0 results | Check that date filter values are **variables** (`FromDate`/`ToDate`), not typed text. Also verify Media Type filter is set correctly |
| "Save File" fails or shows error | Re-open Files app, reconnect to SMB, then re-select the folder inside the shortcut |
| Shortcut freezes or stops mid-transfer | Screen locked — set Auto-Lock to **Never** before running |
| Files save to wrong location | Edit shortcut → Save File action → re-select correct SMB network folder |
| SMB share not visible in Files picker | Open Files app → Connect to Server first, then return to shortcut |
| Transfer is very slow | Normal — SMB over Wi-Fi copies one file at a time. Videos also require encode step. For bulk transfers consider using the USB PowerShell method |
| Can photos and videos copy in parallel? | ❌ iOS Shortcuts is single-threaded — no parallel/async execution is possible. The 2-pass approach is the closest optimisation: photos copy first (fast), then videos |

---

## 💡 Bonus: iOS 26 Automation (Run Automatically on a Schedule)

> You can have this shortcut run **automatically every night** without tapping anything.

1. Open Shortcuts → tap **"Automation"** tab (bottom)
2. Tap **`+`** → **"New Automation"**
3. Choose trigger: **"Time of Day"** → set to e.g. **2:00 AM**
4. Choose **"Run Immediately"** and toggle off **"Ask Before Running"**
5. Add action: **"Run Shortcut"** → select **📷 Copy Photos to PC**
6. Set the date inputs to relative values like **"Yesterday"** as the from/to date

This way your photos back up to your PC automatically every night while you sleep! 🌙
