# COA Page Project — Full Handoff Note

**Date:** May 15, 2026
**Project:** Self-hosted, QR-code-driven Certificate of Analysis (COA) page for Laredo Smoke Shops
**Status:** Live and working with one COA. 44 renamed PDFs prepped and waiting to be uploaded.

---

## TL;DR — Where things stand

The COA page is **live at https://laredosmokeshop.github.io/coas/** and a QR code points to it. One COA is currently uploaded. A folder of 44 renamed-and-cleaned PDFs is sitting on the desktop ready to be bulk-uploaded to GitHub. **One step away from being fully done.**

### The single open task

Upload these to GitHub:
1. The updated `index.html` from `Laredo Smoke Shops coa's/renamed/index.html` → goes in the **root** of the repo (overwrites existing)
2. All 44 PDFs from `Laredo Smoke Shops coa's/renamed/` → goes inside the **`coas/`** folder of the repo

Both happen at https://github.com/laredosmokeshop/coas via "Add file → Upload files". Drag, commit, done. ~5 minutes total.

---

## Credentials and URLs

| Thing | Value |
|---|---|
| Live COA page | https://laredosmokeshop.github.io/coas/ |
| GitHub repo | https://github.com/laredosmokeshop/coas |
| PDF folder inside repo | https://github.com/laredosmokeshop/coas/tree/main/coas |
| GitHub username | `laredosmokeshop` |
| GitHub email | rivasantiago278@gmail.com |
| GitHub password | (stored separately — set during signup) |

---

## Files on disk

All under `~/Documents/Documents - Santiago's Mac mini/Laredo Smoke Shops coa's/`:

- **Original messy-named PDFs** in the root of that folder (43 PDFs + 1 JPG). Untouched. Keep as backup until upload is verified.
- **`renamed/`** subfolder — contains:
  - `index.html` (the updated page that supports the new filename format — overwrites the one currently on GitHub)
  - 44 properly-named PDFs in `Product-Name__YYYY-MM-DD.pdf` format
- **`COA LOG FILLED.xlsx`** and **`COA LOG FOR MERCHANT.xlsx`** — the source of truth for product names and test dates. The Merchant log is cleanest.
- **`laredosmokeshop-coa-qr.svg`** — vector QR code for printing on packaging/labels
- **`laredosmokeshop-coa-qr.png`** — 1056×1056 PNG of the QR for digital use

---

## How the system works (technical design)

**The page is a single static HTML file** (`index.html`) hosted on GitHub Pages. There's no backend, no database, no CMS.

When a customer scans the QR and hits the page, the page's JavaScript calls the **GitHub Contents API** (`https://api.github.com/repos/laredosmokeshop/coas/contents/coas`) to get the list of PDFs in the `coas/` folder. It parses each filename, groups them by product, sorts batches newest-first, and renders the list. Tapping an entry opens the PDF directly from GitHub's CDN.

This means:
- **Adding a COA = uploading a PDF.** No deployment needed. The page reads the folder live.
- **Filenames are the metadata.** Product name + date are encoded into the filename. No JSON config to maintain.
- **Zero recurring cost.** GitHub Pages is free for public repos. No bandwidth limits that matter for this use case.
- **The QR URL never changes.** Same QR can stay on packaging forever.

### Filename format

Two formats supported by the parser:

```
ProductName__YYYY-MM-DD.pdf            (2-part — what we're using)
ProductName__BatchID__YYYY-MM-DD.pdf   (3-part — original design, supports batch IDs)
```

Rules: hyphens for spaces in product names, double-underscore (`__`) as separator, `YYYY-MM-DD` date.

Examples:
- `Blue-Nerdz__2025-09-17.pdf`
- `LA-Kush-3__2025-07-03.pdf`
- `Acai-Gelato__2026-02-03.pdf`

The page displays "Tested Sept 17, 2025" for 2-part files, "Batch X · Sept 17, 2025" for 3-part files.

---

## Decisions made along the way (and why)

- **GitHub Pages over Netlify/Vercel/Drive.** Permanent URL, free, never expires, no signup beyond GitHub itself. Drag-drop updates via GitHub web UI work for non-developers.
- **Parse filenames at runtime via GitHub API instead of maintaining a JSON manifest.** Means adding a new COA is literally just dropping a PDF — no config to edit, no extra step.
- **Skipped batch numbers.** The Excel log doesn't have a batch number column. Batch IDs (like `JAN0326B-POT`) are visible inside the Badger Labs PDFs but render as graphics, not text — extracting them would require OCR per file with fallback for failures. Cost/value didn't justify it. Page falls back gracefully to "Tested [date]".
- **Used the merchant Excel log as source of truth** for product name standardization. Filename strings on disk had inconsistencies (`bmue nerds.pdf`, `Sour cookie.pdf`, `gary payton exotic.pdf`) — the Excel had the canonical names.
- **Converted `zashimi.jpg` to PDF** rather than supporting JPG separately. Keeps the page parser simple and gives consistent UX.
- **Wrote renamed files to a separate `renamed/` folder** instead of in-place renaming, so originals stay safe until upload is verified.

---

## Adding new COAs going forward (the daily workflow)

1. Get a new COA PDF from the lab
2. Rename it: `Product-Name__YYYY-MM-DD.pdf` (hyphens for spaces, no batch needed)
3. Go to https://github.com/laredosmokeshop/coas/tree/main/coas
4. Click **Add file → Upload files** → drag the PDF in → commit
5. Wait 30–60 seconds — it's live for any customer who scans the QR

Multiple PDFs can be dragged in at once.

---

## QR code

- **PNG** (`laredosmokeshop-coa-qr.png`) — 1056×1056px. For digital use: social, email signatures, websites.
- **SVG** (`laredosmokeshop-coa-qr.svg`) — vector. For printing on packaging, labels, signs. Stays sharp at any size.

Minimum print size for reliable scanning: ~1 inch / 2.5 cm per side. The QR will keep working forever as long as the GitHub username and repo name don't change.

---

## Troubleshooting reference

| Problem | Fix |
|---|---|
| New PDF not showing up | Check filename: double `__` separator, `YYYY-MM-DD` date format |
| Page says "Couldn't load COAs" | In `index.html`, verify `const GITHUB_USER = "laredosmokeshop";` is intact |
| QR won't scan | Print bigger (min 1 inch); use SVG version for sharpness |
| Want to change page heading or wording | Edit `index.html`, find `<h1>Certificates of Analysis</h1>`, edit, commit |
| Need to remove a COA | Click the file in the `coas/` folder on GitHub → trash icon → commit |

---

## Possible future enhancements

Not needed for v1, but easy to add later:

- Logo + brand colors on the page
- Filters (by lab, by date range)
- THC%/CBD% preview shown directly on the page (data is in the Excel log, could be auto-injected)
- Custom domain (e.g. `coas.laredosmokeshops.com` instead of the github.io URL — small DNS setup)
- Per-product subpages with their own QR codes (one QR per strain instead of one QR for everything)
- Password protection / private COAs
- Email notification when a new COA is uploaded
- Auto-rename script that runs against the Excel log (so future COAs can be renamed in bulk like we did)

---

## Conversation history summary (for context)

1. **Planning phase.** User wanted a QR-code-driven, continuously-updatable COA page. Clarified: GitHub Pages hosting, organization optimized for client clarity, minimal branding, drag-and-drop update flow, generate QR included.
2. **Build phase.** Created `index.html` (parser + GitHub API fetcher), `qr-generator.html` (standalone QR tool), `SETUP.md` (step-by-step guide).
3. **Live setup phase.** Walked user through GitHub account creation, repo creation, file upload, folder creation via `.gitkeep` trick, editing `GITHUB_USER` value, enabling GitHub Pages. Site went live.
4. **QR generation.** Generated PNG (1056×1056) and SVG QR codes pointing to the live URL.
5. **Bulk rename phase.** Granted folder access, discovered Excel log with all metadata, matched 44/44 files, renamed them into `renamed/` subfolder, converted `zashimi.jpg` to PDF, updated `index.html` to support 2-part filenames.
6. **Upload phase.** Started but paused — user was tired, will resume later.

---

## Quick-resume prompt for next session

When picking this back up, start with: **"Let's finish the COA upload — the renamed folder is ready, just need to push it to GitHub."** Claude can then walk one click at a time through the 2-step upload (overwrite `index.html` in repo root, then upload all 44 PDFs into `coas/` folder).
