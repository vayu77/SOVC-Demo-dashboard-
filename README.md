# SOVC Live Dashboard — Vercel deploy guide

This is a single static page (`index.html`) — no build step, no server, no framework.
It reads your Google Sheet directly in the visitor's browser, so it's free to host and
updates the moment you add a new row.

## Step 1 — Publish your Google Sheet as CSV

1. Open your live Google Sheet.
2. Click **File → Share → Publish to web**.
3. In the first dropdown, select the **2026 SEP** sheet specifically (not "Entire document").
4. In the second dropdown, choose **Comma-separated values (.csv)**.
5. Click **Publish** → confirm.
6. Copy the link it gives you (looks like `https://docs.google.com/spreadsheets/d/e/.../pub?output=csv`).

This makes only that one tab readable as plain data — it does not make your sheet editable
or expose Sheet2/Sheet3, and you can un-publish it at any time from the same menu.

## Step 2 — Point the dashboard at your sheet

Open `index.html`, find this block near the bottom, and paste your link:

```js
const CONFIG = {
  CSV_URL: "PASTE_YOUR_PUBLISHED_CSV_LINK_HERE",
  REFRESH_MS: 5 * 60 * 1000
};
```

Until you do this, the page shows your 3 real sample rows as demo data so you can see the
layout working immediately.

## Step 3 — Deploy to Vercel

**Easiest path (no GitHub, no CLI):**
1. Go to [vercel.com](https://vercel.com) and sign in (free tier is enough).
2. Click **Add New → Project**.
3. Choose **Deploy without Git** / drag-and-drop, and drop this whole folder
   (`index.html`, `vercel.json`) onto the upload area.
4. Click **Deploy**. You'll get a live URL in under a minute
   (e.g. `sovc-dashboard.vercel.app`).

**If you'd rather connect it to GitHub (recommended for future edits):**
1. Create a new GitHub repo and push these files to it.
2. In Vercel, **Add New → Project → Import Git Repository**, select the repo.
3. Leave all build settings blank/default (this is a static site, no framework).
4. Click **Deploy**.
5. Any future `git push` automatically redeploys the site.

## How "live" it actually is

The page fetches your CSV link on load, and again every 5 minutes automatically
(there's also a manual **Refresh** button). Changing the number in
`REFRESH_MS` changes that interval — e.g. `2 * 60 * 1000` for every 2 minutes.
It is not instant/real-time (there's no push notification when a row changes),
but for a daily-entry sales sheet this is effectively live.

## What's shown

- KPI row: total calls, completed, pending, orders sold, orders pending, conversion %
- Agent leaderboard, ranked by orders then calls, with a live conversion-rate color code
- Video call status and order status breakdown (donut charts)
- Product category performance

## Known limitation (matches your current sheet)

`Order status` currently only has `Sold` / `Pending` as options, so there's no full
funnel or drop-off view yet — that requires expanding the dropdown in your sheet
(e.g. adding `Payment Pending`, `Cancelled`, `Not Interested`). The dashboard code
doesn't need to change for that — it reads whatever values exist in the column.
