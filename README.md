# PASTALAVISTA 2026 🍝

Mobile-first trip app for the Kleeh + Tann families' Italy trip, **Jul 17–26 2026** (Rome → San Gimignano → Cetona → Rome).

Single-file site — `index.html` with inline CSS/JS, no build step, no frameworks. Built to load fast on rural 4G.

## Features

- **Day tabs** (Fri 17 → Sun 26) + Reference 📋 + Pending ⏳ screens; auto-opens **today** during the trip
- **Locked days** render as timelines; **open days** render as pick-one card menus (collapsible, with "being finalized" badges until v2 research lands)
- Every card has one-tap **🗺️ Maps / 🚗 Directions / 📞 phone** links that open native apps
- **Reference screen**: every booking code (tap to copy), address, phone, and the Vatican voucher PDF
- **Pending checklist** persists locally on each phone
- Add-to-homescreen ready: `apple-touch-icon`, theme color, standalone-capable

## Deploying

GitHub Pages: Settings → Pages → Deploy from branch → select branch, root folder. That's it — `index.html` is served as-is.

## Files

| File | What |
|---|---|
| `index.html` | The whole app |
| `icon-180.png` / `favicon-32.png` | Home-screen + tab icons (sun over Colosseum arcade) |
| `vatican-voucher.pdf` | Official Vatican Museums voucher — Sat Jul 18, 10:00, 8 pax |

## Notes for v2

- Open-day cards (Jul 20–21, 23–24) carry placeholder hours/prices pending the v2 research handoff
- Card photos hotlink Wikimedia Commons and lazy-load over the styled fallback header; if a file URL 404s the design degrades gracefully — swap in verified URLs in `MENU_PODERI` / `MENU_CETONA` when convenient
