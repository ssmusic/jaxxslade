# JAXX SLADE PICTURES — front-door site

The public landing page for Jaxx Slade. **This is the link you hand to a supervisor / studio-marketing lead / editor.** It presents the catalogue as a B-movie studio's 24-feature slate and funnels every click into SourceAudio, which is the engine behind it.

**Aesthetic:** deliberate first-generation / GeoCities web — rainbow WordArt logo, a terrible blinking banner ad, `<marquee>` crawls, beveled buttons, a Winamp-style "jukebox," tiled starfield, Times + Impact + Courier. The crude chrome is the brand; the real B-movie posters and the live catalogue do the talking. (Same spirit as metalwood.studio, pushed further.)

---

## Files
```
index.html               ← the whole site (HTML + CSS + JS inline)
album_tracks.js          ← all 24 albums × every primary cue + its embed code (200 cues)
jukebox_tracks.js        ← generated public playlist titles + embed codes (no secret)
scripts/sync-jukebox.mjs ← syncs the published SourceAudio jukebox playlist
mint_embed_codes.sh      ← regenerates album_tracks.js from the SourceAudio API
covers/                  ← 24 album covers, JXS0NN_slug.png  (+ manifest.txt)
assets/logo_wordart.png  ← the rainbow WordArt logo
assets/og.png            ← social share card
README.md                ← this file
```
No build step, no dependencies. Open `index.html` and it runs.

## Put it online
Plain static files — drag the folder onto **Netlify Drop**, or use **Cloudflare Pages / GitHub Pages** (framework preset: None). Local preview: `python3 -m http.server 8137` then open `http://localhost:8137`.

---

## Embedded audio — real music plays inline, two ways
1. **The Jukebox** — generated from the published SourceAudio playlist `JXS Selects` (currently 14 tracks). Reorder, add, or remove tracks in SourceAudio; the sync workflow updates the site in the same order. “Echoes of the Nebula” loads by default. Track titles load the inline player, while SOURCEAUDIO ↗ opens the matching SourceAudio search in a new tab so the Jaxx Slade page stays open.
2. **Every album poster** opens a **whole-album pop-up** — a mini-jukebox of that album's primary cues (all 200 cues live in `album_tracks.js`, keyed by album code). Browse ◄ PREV / NEXT ► through all 24, click any cue to load it, "license all NN cues" button to SourceAudio.

How it works: SourceAudio's main pages block framing, but its official **per-track** embed widget (`embed.php?code=<hash>`) is frameable. Codes were minted in bulk via the API.

**To regenerate / refresh codes** (e.g. new releases): run `mint_embed_codes.sh` with your SourceAudio admin API token — it rewrites `album_tracks.js`. Edit the cue list inside that script to add/remove cues. (One cue at a time can also be grabbed from a track's Details → `</>` embed icon.)

**Note — playback is click-to-load + tap-play.** The widget is SourceAudio's licensed, *watermarked* player; it has no autoplay and can't be auto-started from the page (cross-origin). That's intentional for a licensing site — you're not hosting free, full-quality master downloads.

The **Find-a-Cue** search box and deep-links still hand off to the live catalogue for the full 2,643-cue library.

### Keeping the jukebox in sync

The GitHub Action in `.github/workflows/sync-jukebox.yml` runs every 15 minutes and can also be run manually. It looks up the published SourceAudio playlist by name, obtains official per-track embed codes, updates `jukebox_tracks.js`, commits the changed playlist, and deploys the refreshed static site.

One-time setup:

1. In SourceAudio, publish `JXS Selects` and manage its track order there.
2. Create a SourceAudio API token that can read published playlists and obtain track embed codes.
3. In the GitHub repository, add that token as an Actions repository secret named `SOURCEAUDIO_API_TOKEN`. Never put the token in this repo or in `jukebox_tracks.js`.
4. Run **Sync SourceAudio jukebox** once from GitHub Actions. After that, scheduled runs keep it current.

If the playlist is renamed, either rename it to `The Jaxx Slade Jukebox` or set the Actions variable `SOURCEAUDIO_JUKEBOX_PLAYLIST_NAME`. The optional variable `SOURCEAUDIO_JUKEBOX_PLAYLIST_ID` pins the sync to a playlist ID and survives future renames.

## Before it goes live — 2 things to fill in
1. **Domain** — where it lives (then I make the share-card URLs absolute).
2. **Contact email** — the footer uses a placeholder `webmaster@jaxxslade.com`; swap in the real one.

(Not in this version: a **Placements / "where you've heard it"** section. Send 3–5 real placements and I'll add a first-gen "★ NOW PLAYING IN THEATERS ★" block — nothing fabricated.)

## Editing the catalogue
All 24 entries are in the `SLATE` array near the bottom of `index.html` — `{id, code, title, year, n, img, tag, f (filter genres), log (logline)}`. The grid, text-only list, jukebox and bottom crawl all regenerate from it. Covers live in `covers/` (filenames must match `img`); `covers/manifest.txt` has the original SourceAudio URLs for re-pulling.

## Built in
WordArt logo · blinking banner ad · two `<marquee>` crawls (news ticker + bottom album crawl) · Winamp-style jukebox · live SourceAudio search · genre filter · Posters/Text-only views · visitor counter, webring & Netscape badges · responsive enough for phones · no external requests (system fonts, local images).
