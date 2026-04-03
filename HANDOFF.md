# Bobby's Cosmopolitan Lifestyle — Developer Handoff

## Project Overview

This is a personal music archive website for a user who creates one YouTube playlist per month. The site showcases these playlists with thumbnails, tracklists, and links. It is a static HTML file with no server or framework.

---

## Files

| File | Description |
|---|---|
| `index.html` | The complete website — single self-contained HTML file |
| `playlists.json` | Master data file — all playlist metadata and track listings |

---

## Site Design

**Title:** Bobby's Cosmopolitan Lifestyle
**Subtitle:** A Restaurant of Music
**Background:** Black (`#0a0a0a`)
**Text:** Off-white (`#f0ece4`)
**Accent:** Gold (`#c9a84c`)
**Fonts:** Cinzel Decorative (headings/labels) + Libre Baskerville (body/tracks)

### Layout
- Full-width page, max 1400px centered
- Playlists grouped by year, newest year first
- Within each year, playlists ordered newest first (most recent month at top)
- Responsive CSS grid: `repeat(auto-fill, minmax(280px, 1fr))`
- Each playlist is a **card** containing:
  - A 16:9 thumbnail image (links to YouTube playlist, play button on hover)
  - A gold label showing the month name
  - A scrollable tracklist (max 220px tall)

### Thumbnail approach
- Uses **YouTube's oEmbed API** — the only reliable method that works from hosted pages
- On page load, a JavaScript script fetches `https://www.youtube.com/oembed?url=...&format=json` for each playlist
- YouTube returns a `thumbnail_url` field which is the official thumbnail — no hotlinking, no CORS issues
- Thumbnails load in batches with an 80ms stagger to avoid rate limiting
- A dark gradient placeholder shows while thumbnails are loading
- Works on GitHub Pages, Netlify, or any static host
- Does NOT work from `file://` (local desktop) because `fetch()` is blocked for cross-origin requests from file:// — use `python3 -m http.server 8000` to test locally
- YouTube CDN: `https://i.ytimg.com/vi/{VIDEO_ID}/mqdefault.jpg`
- The VIDEO_ID is the first video in each playlist
- Images use `referrerpolicy="no-referrer"` to avoid hotlink blocking
- **Important:** Thumbnails only load when the page is served from a real URL (http:// or https://). They will NOT load from a local `file://` path due to browser security restrictions.
- To test locally: run `python3 -m http.server 8000` in the project folder, then open `http://localhost:8000`
- For production: host on GitHub Pages, Netlify, or any static host

---

## playlists.json Structure

```json
{
  "meta": {
    "title": "Bobby's Cosmopolitan Lifestyle",
    "subtitle": "A Restaurant of Music"
  },
  "playlists": [
    {
      "id": "2026-03",
      "label": "March",
      "year": 2026,
      "url": "https://www.youtube.com/playlist?list=PLs2RfrJzU5ylXpq5mPZ4xUoQk8Jc7CpGV",
      "thumbnail": "https://i.ytimg.com/vi/dwJyv4cGxYI/mqdefault.jpg",
      "tracks": ["Track 1", "Track 2", "..."]
    }
  ]
}
```

**Field notes:**
- `id` — unique string key, format `YYYY-MM` for monthly playlists, or descriptive for older seasonal ones
- `label` — display name shown on the card (e.g. "March", "Fall 2022 — Part 3")
- `year` — integer, used for grouping
- `url` — full YouTube playlist URL
- `thumbnail` — YouTube CDN URL using the first video's ID
- `tracks` — array of strings, track titles as scraped from YouTube

---

## Playlist Coverage

| Year | Playlists | Notes |
|---|---|---|
| 2026 | Jan, Feb, Mar | Ongoing — add new months as they appear |
| 2025 | Jan–Dec | Complete |
| 2024 | Jan–Dec | Complete |
| 2023 | Vol. 1, 1.2, 2, 3, 4, 5, 6, 7 + Summer Mix | Pre-monthly-convention era, volumetric numbering |
| 2022 | Spring, Summer, Fall, Fall Pt 2, Fall Pt 3, Winter | Seasonal naming convention |
| 2021 | Spring, Summer, Fall, Winter | Seasonal naming convention |
| 2020 | Fall, Winter | Earliest entries; `summer 2020` exists as 1-video placeholder |

**Total: 48 playlists**

---

## How the Site Was Built

- HTML/CSS/JS — no framework, no build step
- Playlist data collected by navigating to each YouTube playlist page in a browser and reading the page text
- Thumbnails sourced from YouTube's image CDN using the first video ID in each playlist
- Track listings scraped from YouTube playlist pages
- The site is regenerated from `playlists.json` using a Python script embedded in the conversation history

---

## Known Issues / To-Do

- **`summer 2020`** — exists on the channel with only 1 video, not currently included in the site. Confirm with user whether to add or ignore.
- **Track sort order** — YouTube shows playlists sorted by "Manual" order by default. Some older playlists may have been sorted differently when scraped. Worth spot-checking if track order matters.
- **Thumbnails on file://** — will not load when opening index.html directly from desktop. Must use a local server or hosted URL.
- **Track listings incomplete** — a handful of tracks may be missing from older playlists where videos were unavailable/private at time of scraping.

---

## Suggested Next Features

- **Hosting** — deploy to GitHub Pages or Netlify so thumbnails work without a local server
- **Track listing display** — currently a scrollable list under each card; could be shown as a modal/drawer on click instead
- **Search** — filter playlists or tracks by keyword using the JSON data
- **New playlist workflow** — when a new month's playlist is created, add its entry to `playlists.json` and regenerate `index.html` from the Python script
- **About / bio section** — short intro text above the grid
- **Color theme toggle** — light/dark mode

---

## How to Add a New Playlist

1. Open `playlists.json`
2. Add a new entry at the top of the `playlists` array:
```json
{
  "id": "2026-04",
  "label": "April",
  "year": 2026,
  "url": "https://www.youtube.com/playlist?list=PLAYLIST_ID_HERE",
  "thumbnail": "https://i.ytimg.com/vi/FIRST_VIDEO_ID/mqdefault.jpg",
  "tracks": ["Track 1", "Track 2"]
}
```
3. Rebuild `index.html` by running the Python generation script (see conversation history), or ask Claude to regenerate the site from the updated JSON.

---

## Rebuilding index.html from playlists.json

Ask Claude:
> "Using the playlists.json file, regenerate index.html for Bobby's Cosmopolitan Lifestyle. The design uses Cinzel Decorative and Libre Baskerville fonts, black background, gold accents. Each playlist is a card with a thumbnail linking to YouTube, a gold month label, and a scrollable tracklist. Playlists are grouped by year, newest first, with a year subheading in gold. The subtitle is 'A Restaurant of Music'."
