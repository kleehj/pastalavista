# Trip App — Build Spec & Reuse Prompt

*Paste this into a **new Claude Code (code) chat** first. It sets up the whole design system.
Then paste your **SCHEDULE HANDOFF** (the itinerary/research from your planning chat).
The assistant acknowledges this spec, does any setup it can, then **waits** for the schedule.*

---

## Your job (assistant, read this first)

You're building a **single-file, mobile-first trip web app** hosted on **GitHub Pages**, in the style of a boutique travel guide. This document is the **design system and technical spec** — reuse all of it. When you've absorbed it:

1. Confirm you understand, in a few lines.
2. Ask me only the **essential setup questions** you can't infer (see "Setup questions" below).
3. Then **stop and wait** — I'll paste a **`SCHEDULE HANDOFF`** with the itinerary, lodging, bookings, photos, etc.
4. Build the app from that handoff using this spec. Commit in logical chunks and push to the working branch. Verify each change headless before moving on.

Don't start inventing an itinerary — wait for the handoff. Where the handoff is missing build-critical facts (addresses, coordinates, times), ask a short batch of questions, then proceed with clearly-labeled placeholders.

## Setup questions (ask these up front, keep it short)

- Repo / branch to build on (or create one)?
- Trip name + a color theme (or propose one that fits the destination — warm/boutique, not generic).
- Temperature units (°F/°C) and any group nicknames.
- Confirm the **privacy posture** (see Hosting): the repo is public, so anyone with the URL can read everything (door codes, booking links). We use `noindex` + an obscure URL, not real security. OK, or omit the most sensitive bits?

---

## What we're building

- **One self-contained `index.html`** — inline CSS + JS, **no frameworks, no build step**. Everything in data objects + small render functions so it's trivial to edit.
- Deployed on **GitHub Pages** from the repo root (`.nojekyll` present). Must load fast on **rural/spotty 4G**.
- **Mobile-first, one-hand use, big tap targets.** Desktop is a bonus.
- Aesthetic: warm, boutique-hotel feel. Display font for headers + clean sans for body via Google Fonts (we used **Fraunces** + **Inter**). Soft shadows, rounded cards, sticky header.
- **Add-to-home-screen ready**: generated `apple-touch-icon`, `theme-color`, `apple-mobile-web-app-capable`, standalone metas.

## Hosting, privacy & deploy

- Serve `index.html` from repo root. Add `.nojekyll`.
- **Keep it off search**: `<meta name="robots" content="noindex, nofollow">` (optionally a `robots.txt` too).
- **Privacy reality**: a public repo + public Pages means anyone with the link (door PINs, booking `auth_key` links, payment amounts) can see it. `noindex` only reduces discovery. Flag sensitive items to me each time and confirm whether to include them.
- Deploy = commit + push; enable Pages (Settings → Pages → branch, root). **Note the 1–2 min build lag** and use a `?v=N` cache-buster when testing so you're not looking at a stale build.

## Navigation & the header

- **Sticky header**: brand wordmark + a gold **Today** button.
- **Today button does two jobs**: (a) jumps to today's day screen, and (b) **hard-refreshes** to defeat iOS home-screen caching — `location.replace(location.pathname + '?r=' + Date.now() + '#' + todayId)`. This is the single most important UX fix for a home-screen web app; plain reloads serve stale cache.
- **Cold open → today**; a refresh-reload (`?r=` present) keeps the screen you were on. Show the Today button only during the trip window (auto-detect from device date).
- **Day tabs**: horizontal scroll. Insert **gold "special" tabs** (dinner / evening / self-guided walk) *between* day tabs where useful.

## The screen/card toolkit (mix per day)

- **Locked timeline day** — ordered rows: time · title · note · ⚠️ warn · action buttons. Supports **two tracks (A/B)** for split groups (e.g. two families on different flights).
- **Pick-one card menu** — options as cards: hero photo, one-line pitch, meta row (drive time / hours / price), expandable details, warnings, map/directions links. Use `"being finalized"` / `"photo coming · <subject>"` labeled placeholders until data/photos land.
- **Fixed single-activity day** — one auto-expanded card (when the day is locked to one thing).
- **Dinner / evening screen as its own tab** — restaurant "spots" each with rating, closure badge (e.g. *Closed Tue*), a tag (*casual · value*, *kids 👍*), and per-spot **Call + Map** buttons. Order by preference (numbered), and surface conflicts loudly (e.g. a "Tuesday closures" banner).
- **Self-guided WALK card** — connected stops with markers. **Use LETTERS (A, B, C…) that match Google Maps' multi-stop waypoint labels**, plus one combined walking-route link (origin + waypoints + destination + `travelmode=walking`). Give off-route steps (a taxi hop, dinner) distinct non-letter markers so they read as "not on the walking route."
- **Reference screen** — everything in one place: **tap-to-copy** booking codes, addresses, `tel:`/`mailto:`, linked **voucher PDFs**, a cash checklist, and **QR codes for key reservation refs/vouchers** (so they scan at the venue).
- **Pending checklist** — open to-dos, persisted in `localStorage`.

## Per-day header chips

Render a chip row under each day title:
- **TODAY** (when it's today), **live weather** (high/low °F + icon), **🏠 home** (Maps to that night's lodging), **🚕 Uber-home** (cities only), lock status, and a heat/weather advisory when relevant.

## Link & integration patterns (keep all of these)

- **Google Maps place link** — prefer a **verified `place_id`**: `https://www.google.com/maps/search/?api=1&query=<Name>&query_place_id=<ID>`. Fall back to `https://maps.google.com/?q=<Name+City>` when there's no id. **Never invent** website/TripAdvisor URLs.
- **Google Maps directions** — `.../maps/dir/?api=1&origin=…&destination=…&travelmode=walking|driving`. For **multi-stop tours** add `&waypoints=A%7CB` and letter the guide stops to match Google (A = origin, then B, C…).
- **Uber deep link, current location → any destination** — `https://m.uber.com/ul/?action=setPickup&pickup=my_location&dropoff[latitude]=<LAT>&dropoff[longitude]=<LNG>&dropoff[nickname]=<Name>`. Opens Uber routed from wherever the rider is. Use for "Uber home" and any city stop. (**Cities only** — Uber doesn't serve rural areas; note **FreeNow** where it's the cheaper regulated-taxi option.)
- **Phones/emails** — `tel:` and `mailto:`. **Confirmations** — link Booking.com / TheFork confirmation URLs and commit voucher **PDFs** into the repo, linked from the relevant day + Reference.
- **Weather** — **Open-Meteo**, client-side, free, no key, CORS-friendly: `https://api.open-meteo.com/v1/forecast?latitude=<csv>&longitude=<csv>&daily=weather_code,temperature_2m_max,temperature_2m_min&temperature_unit=fahrenheit&timezone=…&forecast_days=16`. One call for all base locations; map each day → a location + date. Cache in `localStorage` ~3h; hide the chip gracefully if offline or a date is outside the forecast window (keep a static heat/weather advisory as fallback).

## Photos

- **Commit real photos** into `photos/` (do **not** hotlink). Downscale to ~1600px max, progressive JPEG, ~150–500 KB each (they were 30 MB+ raw — that kills rural 4G). Respect EXIF rotation.
- Lazy-load, `object-fit: cover`, and **graceful `onerror`** fallback to a gradient. Support a hero image + an optional caption **gallery strip** of a day's sub-stops.
- Until real photos arrive, show `"📷 photo coming · <subject>"` labeled placeholders (don't leave broken images).

## Build & verify workflow

- Generate the app icon and downscale photos with **PIL/Pillow**.
- **Verify every change headless** (Playwright + the preinstalled Chromium) at an iPhone viewport: assert no JS errors and screenshot the affected screen before committing.
- Commit in small logical chunks with clear messages; push to the working branch.

---

## Appendix — proven helper snippets (start from these)

```js
// --- Maps / directions / Uber ---
const mapsQ  = q => 'https://maps.google.com/?q=' + encodeURIComponent(q);
const mapsPid = (name,id) => id
  ? 'https://www.google.com/maps/search/?api=1&query=' + encodeURIComponent(name) + '&query_place_id=' + id
  : mapsQ(name);
const dirTo = (o,dst,mode) => 'https://www.google.com/maps/dir/?api=1&origin=' + encodeURIComponent(o)
  + '&destination=' + encodeURIComponent(dst) + '&travelmode=' + (mode||'driving');
// multi-stop: append '&waypoints=' + encodeURIComponent(a) + '%7C' + encodeURIComponent(b)
const uberTo = (lat,lng,name) => 'https://m.uber.com/ul/?action=setPickup&pickup=my_location'
  + '&dropoff[latitude]=' + lat + '&dropoff[longitude]=' + lng + '&dropoff[nickname]=' + encodeURIComponent(name);

// --- iOS-cache-proof refresh + open-to-today ---
function hardRefresh(hash){ location.replace(location.pathname + '?r=' + Date.now() + (hash||'')); }
// Today button: hardRefresh('#'+TODAY).  On load:
const isRefresh = /[?&]r=/.test(location.search);
show(isRefresh ? (location.hash.slice(1) || TODAY || FIRST) : (TODAY || location.hash.slice(1) || FIRST));

// --- Weather (Open-Meteo), cached ~3h in localStorage, °F ---
// fetch daily weather_code + temperature_2m_max/min for all base lat/lng in one call;
// map {locationKey: {'YYYY-MM-DD': {max,min,code}}}; render a chip per day; hide if missing.
```

```html
<!-- head essentials -->
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="robots" content="noindex, nofollow">
<meta name="theme-color" content="#C05A33">
<meta name="apple-mobile-web-app-capable" content="yes">
<link rel="apple-touch-icon" href="icon-180.png">
```

---

## What I'll send next

A **`SCHEDULE HANDOFF`** containing: trip dates · travelers/groups · lodging (addresses + refs + that-night mapping) · transport · booked activities (with codes/vouchers) · a per-day plan marking which days are **locked timelines**, **pick-one menus**, or **fixed activities** · restaurant shortlists · and any photos/PDFs. Build from it using this spec; ask me a short batch of questions for anything build-critical that's missing.

**Acknowledge this spec, ask your setup questions, then wait for my `SCHEDULE HANDOFF`.**
