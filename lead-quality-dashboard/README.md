# Lead Quality Dashboard

A live, interactive dashboard that reads directly from Google Sheets and renders a full lead quality analysis — no backend, no build step, just a single HTML file hosted on GitHub Pages.

**Live demo:** `https://<your-username>.github.io/lead-quality-dashboard/`

---

## What it shows

| Section | Details |
|---|---|
| **KPI row** | Total leads, closed rate (baseline), broad quality rate, CPL uplift target |
| **Monthly trend** | Closed rate line chart across Apr–Sep 2009 |
| **Status breakdown** | Donut chart of all CallStatus values |
| **Segment tabs** | Six tabs — Traffic source, Ad creative, Debt level, Phone score, Geography, Ad campaign — each with ranked horizontal bar charts and a key insight |
| **Scenario analysis** | Five filter scenarios plotted against the 9.6% quality target |
| **Live filters** | Filter any view by Partner, Debt level, Month, and State simultaneously |

---

## Setup

### 1. Make the Google Sheet public

Open the sheet → **Share** → **General access** → change to **"Anyone with the link"** → **Viewer**. This lets the dashboard fetch the CSV without requiring sign-in.

The sheet ID is already embedded in `index.html`. If you ever move the data to a different sheet, update these two constants at the top of the `<script>` block:

```js
const SHEET_ID = '1Fn9pnxt7JvXYLBT-f8puwNWntCxjfoJwbIBptH_U1Ak';
const GID      = '1511019979';
```

### 2. Create the GitHub repository

```bash
git init
git add .
git commit -m "initial commit"
gh repo create lead-quality-dashboard --public --source=. --push
```

Or create the repo manually on GitHub and push:

```bash
git remote add origin https://github.com/<your-username>/lead-quality-dashboard.git
git branch -M main
git push -u origin main
```

### 3. Enable GitHub Pages

Go to your repo on GitHub → **Settings** → **Pages** → Source: **GitHub Actions**.

The workflow at `.github/workflows/pages.yml` will automatically deploy on every push to `main`. Your dashboard will be live at:

```
https://<your-username>.github.io/lead-quality-dashboard/
```

The first deployment takes about 60 seconds. After that, pushes deploy in under 30 seconds.

---

## How it works

```
Google Sheets (public CSV export)
        │
        │  fetch() on page load
        ▼
  index.html (browser)
  ├── parseCSV()      — lightweight CSV parser, no dependencies
  ├── compute()       — aggregates segments, monthly trend, status counts
  ├── Chart.js 4.4    — trend line + status donut (loaded from cdnjs)
  └── renderAll()     — re-runs on every filter change
```

All processing happens in the browser. No server, no API key, no build pipeline.

---

## Updating the data

The dashboard re-fetches the sheet every time the page loads. To update the data:

1. Add or edit rows in the Google Sheet
2. Reload the dashboard — changes appear immediately

No redeployment needed.

---

## Column reference

The dashboard expects columns in this order (matching the original dataset):

| Col | Field | Used for |
|---|---|---|
| A | LeadCreated | Monthly trend |
| E | CallStatus | Quality classification |
| F | WidgetName | Ad creative segment |
| I | AddressScore | (available, not charted) |
| J | PhoneScore | Phone score segment |
| K | AdvertiserCampaignName | Ad campaign segment |
| L | State | Geography segment |
| M | DebtLevel | Debt level segment |
| O | Partner | Traffic source segment |

---

## Tech stack

- **Zero build tooling** — plain HTML + vanilla JS
- **Chart.js 4.4** — CDN, for the two canvas charts
- **No framework, no bundler, no npm**
- Fully responsive, dark-mode aware

---

## License

MIT
