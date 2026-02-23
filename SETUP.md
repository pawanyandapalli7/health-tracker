# 🏋️ Health Tracker — Setup Guide

Your personal daily routine tracker with GitHub-backed storage and health insights dashboard.

---

## Step 1 — Create a Private GitHub Repo

1. Go to [github.com/new](https://github.com/new)
2. Name it something like `health-tracker`
3. Set it to **Private**
4. **Do NOT** initialize with a README (leave it empty)
5. Click **Create repository**

---

## Step 2 — Upload These Files

In your new empty repo, upload these two files:
- `index.html`
- `data.json`

**How to upload:**
1. On your repo page, click **"uploading an existing file"**
2. Drag and drop both files
3. Click **Commit changes**

---

## Step 3 — Enable GitHub Pages

1. Go to your repo → **Settings** → **Pages** (left sidebar)
2. Under **Source**, select **Deploy from a branch**
3. Branch: `main` / Folder: `/ (root)`
4. Click **Save**

Wait ~1 minute, then your tracker will be live at:
```
https://YOUR_USERNAME.github.io/health-tracker/
```

> **Note:** GitHub Pages works on private repos. The *site* is public (anyone with the URL can view it), but your *source code and data.json* are private. If you want the site itself to be private too, you'll need GitHub Pro — but for a personal health tracker, the URL being obscure is usually enough.

---

## Step 4 — Create a Personal Access Token (PAT)

This lets the app save your data back to the repo automatically.

1. Go to [github.com/settings/tokens/new](https://github.com/settings/tokens/new)
2. Give it a name like `health-tracker`
3. Set expiration to **No expiration** (or 1 year)
4. Under **Scopes**, check ✅ **`repo`** (full control of private repositories)
5. Click **Generate token**
6. **Copy the token immediately** — you won't see it again

---

## Step 5 — Connect the App

1. Open your tracker at `https://YOUR_USERNAME.github.io/health-tracker/`
2. A setup modal will appear
3. Fill in:
   - **GitHub Username**: your GitHub username
   - **Repository Name**: `health-tracker` (or whatever you named it)
   - **Personal Access Token**: paste the token from Step 4
4. Click **Connect & Save**

The app tests your connection, then saves the config to your browser's localStorage. You never have to enter it again (unless you clear your browser data).

---

## How It Works

```
You check a task
    ↓
App waits 1.2 seconds (debounced)
    ↓
Commits updated data.json to your private GitHub repo
    ↓
You open tracker on any device → loads data.json from GitHub
    ↓
All your history is there ✓
```

Every device that has your PAT configured will stay in sync.

---

## Using on Multiple Devices

On each new device (phone, laptop, etc.):
1. Open `https://YOUR_USERNAME.github.io/health-tracker/`
2. Click **⚙ settings** in the top right
3. Enter your username, repo name, and PAT
4. Click Connect — your full history loads instantly

---

## Features

| Feature | Details |
|---|---|
| ✅ Daily Checklist | 10 sections, collapsible, auto-saves |
| 🔥 Streak Counter | Counts days with ≥80% completion |
| 📊 Dashboard | 30-day chart, category breakdown, missed items, heatmap |
| 📝 Daily Notes | Log how you feel each day, stored with your data |
| 🔒 Private Storage | All data in your private GitHub repo |
| 📱 Mobile Friendly | Works on phone and desktop |

---

## Troubleshooting

**"Repo not found or token invalid"**
→ Check your username and repo name are spelled correctly. Make sure the token has `repo` scope.

**"Save failed"**
→ Your token may have expired. Go to ⚙ settings and generate a new one.

**Data not syncing across devices**
→ Make sure you've entered the same token on each device. Check the sync status dot in the nav bar.

**Want to view your raw data?**
→ Go to your GitHub repo and open `data.json` — it's all there, readable JSON organized by date.

---

## Data Format

Your `data.json` looks like this:
```json
{
  "2026-02-21": {
    "checks": {
      "0-0": true,
      "0-1": true,
      "1-0": false
    },
    "note": "Good energy today. Skipped the snack."
  }
}
```

Each key is a date. `checks` maps `sectionIndex-itemIndex` to true/false. You own this data completely.
