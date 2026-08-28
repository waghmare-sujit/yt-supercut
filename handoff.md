# YT Supercut — Handoff Document

**Plugin ID:** `yt-supercut`  
**Author:** Waghmare (`github.com/sujit-waghmare`)  
**Funding:** `paypal.me/waghmaresujit`  
**Version:** 1.2.0  
**Mobile:** ✅ Enabled  
**Files:** `main.js` · `styles.css` · `manifest.json`

---

## Changelog v1.1.0 → v1.2.0

| # | Bug | Fix |
|---|---|---|
| 1 | `![Thumbnail]()` renders 1:1 square | CSS `aspect-ratio: 16/9` added for `.markdown-preview-view img[alt="Thumbnail"]`. Shorts thumbnail wrapped in `<div class="yts-thumb-shorts">` which gets `aspect-ratio: 9/16` |
| 2 | `buildThumbnailMd()` took no `sourceUrl` arg | Updated signature to `buildThumbnailMd(videoId, sourceUrl)` — returns plain markdown for normal videos, shorts-wrapper div for `/shorts/` URLs |
| 3 | Float window only showed in Reading View | Removed `isReading` check from `_floatUpdate` — now works in both Live Preview and Reading View |
| 4 | Float window drag broken (iframe ate mousemove) | Removed drag entirely. Position now controlled by frontmatter `Float X` / `Float Y` (1 unit = 100px). Updates live when frontmatter changes |
| 5 | `_floatCreate` had stale signature | Updated to `_floatCreate(videoId, sourceUrl, fm)` — reads `fm['Float X']` and `fm['Float Y']` |
| 6 | `_floatUpdate` didn't respond to coord changes | Added `xChanged`/`yChanged` checks so window repositions when `Float X`/`Float Y` frontmatter is edited |
| 7 | `_iframeBodyToggle` thumbRegex didn't match shorts wrapper | Updated regex to match both plain `![Thumbnail]()` and `<div class="yts-thumb-shorts">![Thumbnail]()</div>` |
| 8 | `_fetchMetadata` still used hardcoded `![Thumbnail](thumbUrl)` | Updated to use `buildThumbnailMd(videoId, url)` for shorts-aware thumbnail injection |

---

## Frontmatter Properties Used

| Property | Type | Used By |
|---|---|---|
| `YouTube Url` | string | Everything |
| `Thumbnail Url` | string | Metadata fetcher (writes) |
| `Channel UID` | string | Metadata fetcher (reads/writes) |
| `Float Window` | boolean | Floating player on/off |
| `Float X` | number | Float window horizontal pos (1 unit = 100px). Default: 8 |
| `Float Y` | number | Float window vertical pos (1 unit = 100px). Default: 1 |
| `iframe` | boolean | Reflects body state: true = iframe div, false = thumbnail |
| `Sonicfonia` | boolean | Background audio on/off |
| `Timestamp` | boolean | Auto-convert timestamps on save |
| `Repeat` | boolean | Sonicfonia repeat |

---

## Commands (8 total — unchanged from v1.1.0)

| Command ID | Name |
|---|---|
| `yts-toggle-float` | Toggle float window |
| `yts-toggle-iframe` | Toggle iframe embed (thumbnail ↔ iframe in note body) |
| `yts-toggle-thumb-video` | Toggle: Thumbnail ↔ Video URL (cursor line) |
| `yts-fetch-metadata` | Fetch YouTube metadata for current note |
| `yts-convert-timestamps` | Convert (mm:ss)/(hh:mm:ss) to timestamp links |
| `yts-sonicfonia-toggle` | Sonicfonia: Toggle play/stop |
| `yts-sonicfonia-next` | Sonicfonia: Next track |
| `yts-sonicfonia-prev` | Sonicfonia: Previous track |

---

## Feature Details

### 1. Floating Window

**Positioning system:**
- `Float X` and `Float Y` in frontmatter control position. 1 unit = 100px.
- Default when not set: `x=8, y=1` (top-right).
- `_floatUpdate` checks `xChanged || yChanged` — if either differs from `_lastFloatX`/`_lastFloatY`, the window is recreated at the new position.
- This makes repositioning instant: just edit the frontmatter number and save.

**No drag:**
- Drag was removed because the iframe reliably ate mouse events, making it unreliable.
- Frontmatter coordinates are the canonical solution: explicit, persistent, per-note.

**Both views:**
- `isReading` check removed from `_floatUpdate`. The overlay is `position:fixed` on `document.body` — it renders regardless of the current view mode.

**Shorts:**
- `isShortUrl(sourceUrl)` → adds `yts-float-short` class → CSS `aspect-ratio: 9/16; width: 340px`.

**Handle bar:**
- Still present for close button + coord hint label (`⊞ X:8 Y:1`).
- `cursor: default` — no grab cursor since drag is gone.

### 2. iframe Body Toggle

**Thumb regex (updated):**
```js
const thumbRegex = /(?:<div class="yts-thumb-shorts">\s*)?!\[Thumbnail\]\([^)]+\)\s*(?:<\/div>)?\n?/g;
```
Matches both:
- `![Thumbnail](url)` — normal video
- `<div class="yts-thumb-shorts">\n![Thumbnail](url)\n</div>` — shorts

**buildThumbnailMd signature:**
```js
function buildThumbnailMd(videoId, sourceUrl)
```
- Normal: returns `![Thumbnail](img.youtube.com/vi/ID/maxresdefault.jpg)`
- Shorts: wraps in `<div class="yts-thumb-shorts">\n![Thumbnail](…)\n</div>`

### 3. Thumbnail aspect ratio (CSS)

```css
.markdown-preview-view img[alt="Thumbnail"] {
  width: 100%;
  aspect-ratio: 16 / 9;
  object-fit: cover;
  border-radius: 8px;
  display: block;
}
.yts-thumb-shorts img[alt="Thumbnail"] {
  aspect-ratio: 9 / 16;
  max-width: 360px;
  margin: auto;
}
```

Applies in reading view and source/live preview. Does not depend on the URL format — purely CSS on `alt="Thumbnail"`.

---

## Settings Reference

| Setting Key | Default | Notes |
|---|---|---|
| `inlineEmbedPosition` | `'below'` | `'above'` \| `'below'` |
| `sonicfoniaDefaultOn` | `true` | Fallback when no `Sonicfonia` property |
| `sonicfoniaPrimaryLink` | `''` | Fallback URL for Sonicfonia |
| `sonicfoniaApiKey` | `''` | YouTube Data API v3 key (playlist only) |
| `sonicfoniaShuffle` | `false` | Shuffle playlist |
| `sonicfoniaRepeatEnabled` | `false` | Finite repeat toggle |
| `sonicfoniaRepeatCount` | `3` | Repeat count (1–10) |
| `ytAudioTitleColor` | `'#8fa0ba'` | ytaudio title color |
| `ytAudioBoldTitle` | `false` | Bold title in ytaudio |
| `ytAudioThumbColor` | `'#ffffff'` | Progress bar thumb color |
| `ytAudioTrackColors` | 5-color array | Progress bar gradient |

> Note: `floatPosTop`, `floatPosLeft` settings keys removed in v1.2.0 — replaced by frontmatter `Float X`/`Float Y`.

---

## File Structure

```
yt-supercut/
├── main.js        ← All plugin logic
├── styles.css     ← Float window, youtube-container, thumbnail ratio, ytaudio, sonicfonia
├── manifest.json  ← Plugin metadata
├── README.md      ← GitHub readme
└── handoff.md     ← This file
```

### main.js Internal Structure

```
SHARED HELPERS
  extractVideoId
  isShortUrl
  extractPlaylistId / isPlaylistUrl
  shuffleArray
  getFrontmatterValue
  formatTime
  cleanYouTubeUrl
  thumbFromVideoId
  buildIframeHtml(videoId, sourceUrl)     — normal=16:9 div, shorts=9:16 div
  buildThumbnailMd(videoId, sourceUrl)    — normal=plain md, shorts=wrapped div

PLAYLIST FETCHER    fetchPlaylistVideoIds

SONICFONIA IFRAMES  sonicfonia_createIframe / _loadPlaylistInIframe / _removeIframe

DEFAULT_SETTINGS

YTSuperCutSettingTab

YTSuperCutPlugin
  onload / onunload
  loadSettings / saveSettings
  _injectYTApi
  Feature 1   _floatToggleProperty
              _floatUpdate        (checks x/y change as well as videoId change)
              _floatCreate(videoId, sourceUrl, fm)
              _floatRemove
  Feature 2   _iframeBodyToggle
  Feature 3   _thumbVideoToggle
  Feature 4   _fetchMetadata
  Feature 5   _applyTimestampConversion / _tsLiveProcess / _convertTimestamps
  Feature 6   _sfPlayForFile / _sfLoadPlaylist / _sfPlayCurrent
              _sfNextTrack / _sfPrevTrack / _sfStopAudio / _sfTogglePlayback
              _sfOnLeafChange / _sfOnActiveFileMetaChanged / _sfUpdateStatusBar
  Feature 7   _ytAudioBlock
```

---

## Known Limitations / TODOs

- **Float window position** is per-note (frontmatter). Two notes can have different X/Y values — intentional.
- **Float window** does not have a minimum boundary guard on the right/bottom edge — if X/Y values are too large the window may go off-screen. Add clamping in `_floatCreate` if needed.
- **Sonicfonia playlist** track advancement is YouTube-native. `Next/Prev` commands reload the iframe queue (brief audio gap).
- **Metadata description** scraped from `ytInitialPlayerResponse` — YouTube may change the key.
- **Timestamp debounce** 1200ms — fast frontmatter edits may delay conversion.

---

## Adding New Features

1. Add key to `DEFAULT_SETTINGS`.
2. Add UI in `YTSuperCutSettingTab.display()`.
3. Add method to `YTSuperCutPlugin` with `_featureName` prefix.
4. Wire command in `onload()`.
5. Add CSS to `styles.css` under a labelled comment.
6. Update this document: Changelog, Frontmatter table, Commands table, Feature Details, Settings Reference.

---

## Frontmatter Init Template

```yaml
---
YouTube Url: https://youtu.be/VIDEO_ID
Thumbnail Url: ""
Channel UID: ""
Timestamp: true
Sonicfonia: false
iframe: false
Float Window: false
Float X: 8
Float Y: 1
---
```
