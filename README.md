# Fuel Cost Calculator

A tiny **offline** app that calculates:

- **Cost per km** (in cents)
- **Fuel used** for the trip (litres)
- **Total trip cost**

It is a **PWA (Progressive Web App)** — plain HTML/CSS/JavaScript, no build step, no
App Store, no Apple developer fee. It runs in the browser on **iPhone and Android**, and
can be "Added to Home Screen" so it looks and behaves like a native app and works with no
internet connection.

## Inputs

| Field | Example | Unit |
|-------|---------|------|
| Fuel price at the station | `190` | cents / litre (Australian style, so 190 = $1.90/L) |
| Trip distance | `10` | km |
| Vehicle fuel consumption | `5.1` | litres / 100 km |
| From / To | `Home` / `CBD` | free text, optional |

The fuel price is remembered between sessions, so it is already filled in next time
you open the app.

### How it is calculated

```
price per litre = price_cents / 100
cost per km     = (consumption / 100) * price_per_litre
fuel used       = (consumption / 100) * distance
trip cost       = cost per km * distance
```

Example (`190`, `5.1`, `10`): cost per km = **9.69 ¢**, fuel used = **0.51 L**,
trip cost = **$0.97**.

### Remembered places

Every **From** and **To** you save is kept in a list on the device. Tap the small
chevron in either box (or just start typing) to drop down the places used before,
newest first, filtered by what has been typed. Matching ignores case, so `cbd` and
`CBD` stay one entry. The list is capped at 30 and rides along in the backup file.

Trips saved before this existed had a single `Location` instead; they still show
correctly in the history and their locations were folded into the dropdown.

## Backup (export / import)

All saved trips live on the phone in `localStorage`, which is not permanent — clearing
browser data or moving to a new phone loses them. The **Backup** card at the bottom of the
app guards against that:

- **Export** writes every saved trip to `fuel-cost-backup-YYYY-MM-DD.json`. On iPhone this
  opens the share sheet (save to Files, Drive, or mail it to yourself); elsewhere it just
  downloads.
- **Import** reads one of those files back. Trips are merged on their timestamp, so
  importing the same backup twice adds nothing and never creates duplicates — importing
  from two different phones gives you the union of both.

Nothing leaves the device unless you choose to share the file, and the app stays fully
offline.

## Files

```
index.html          The whole app (UI + logic)
manifest.json       PWA metadata (name, icons, colours)
service-worker.js   Enables offline use
icons/              App icons (192px, 512px)
```

## Run it locally (on your Windows PC)

Just open `index.html` in any browser by double-clicking it. That's enough to test the
maths. (The offline service worker only fully activates when served over http/https, e.g.
GitHub Pages below.)

## Put it on GitHub + get a phone link (GitHub Pages)

You only need a free GitHub account. No Mac required.

### 1. Create the repository

1. Go to <https://github.com/new>.
2. Repository name: e.g. `fuel-cost`. Set it to **Public**. Click **Create repository**.

### 2. Upload the files (easiest — no command line)

1. On the new repo page, click **"uploading an existing file"**.
2. Drag in `index.html`, `manifest.json`, `service-worker.js`, **and the `icons` folder**.
3. Click **Commit changes**.

> Prefer Git on the command line? From this folder run:
> ```bash
> git init
> git add .
> git commit -m "Fuel cost calculator PWA"
> git branch -M main
> git remote add origin https://github.com/<your-username>/fuel-cost.git
> git push -u origin main
> ```

### 3. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)**. Click **Save**.
4. Wait ~1 minute. Your app will be live at:
   `https://<your-username>.github.io/fuel-cost/`

## Install on your phone

**iPhone (Safari):**
1. Open the GitHub Pages link in **Safari**.
2. Tap the **Share** button → **Add to Home Screen** → **Add**.

**Android (Chrome):**
1. Open the link in **Chrome**.
2. Tap the **⋮** menu → **Add to Home screen** / **Install app**.

The icon now sits on your home screen and opens full-screen, working offline.

## Updating the app later

Edit the files, re-upload/commit to GitHub, and **bump `CACHE_VERSION`** in
`service-worker.js` (e.g. `fuel-cost-v1` → `fuel-cost-v2`) so phones pick up the new version.
