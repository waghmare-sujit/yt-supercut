<div align="center">

# YT Supercut

**All-in-one YouTube toolkit for Obsidian**

[![Version](https://img.shields.io/badge/version-1.2.0-blue?style=flat-square)](https://github.com/sujit-waghmare/yt-supercut/releases)
[![Obsidian](https://img.shields.io/badge/Obsidian-1.4.0+-purple?style=flat-square)](https://obsidian.md)
[![Mobile](https://img.shields.io/badge/Mobile-Friendly-brightgreen?style=flat-square)](https://obsidian.md)
[![License](https://img.shields.io/badge/License-All_Rights_Reserved-red?style=flat-square)](./LICENSE)

[Features](#features) · [Installation](#installation) · [Usage](#usage) · [Float Window Position](#float-window-position) · [Settings](#settings) · [Commands](#commands) · [FAQ](#faq)

</div>

---

YT Supercut merges seven separate YouTube tools into one plugin: a **floating player** (editing + reading view), **body iframe embed toggle** with correct 16:9 / 9:16 aspect ratios, **thumbnail ↔ video URL swap**, **metadata fetcher**, **live timestamp links**, **Sonicfonia background audio**, and a **custom ytaudio player block**.

No Templater. No separate plugins. One install.

---

## Features

### 🎬 Floating Window (works in editing AND reading view)
- Activates via `Float Window: true` in frontmatter — visible in both **Live Preview and Reading View** so you can take notes while watching
- Position controlled via frontmatter: `Float X` and `Float Y` (1 unit = 100px per axis)
- **Shorts** (`/shorts/` URLs) render in a portrait 9:16 window automatically
- Close button writes `Float Window: false` back to frontmatter

### 🔗 iframe Body Toggle (16:9 / 9:16 aware)
- Replaces `![Thumbnail]()` in your note body with a live embedded player — or reverts it back
- **Normal videos** → `youtube-container` → 16:9 ratio
- **Shorts** → `youtube-container shorts` → 9:16 ratio, max-width 360px
- All `![Thumbnail]` images are CSS-forced to 16:9 by default (9:16 for shorts via wrapper div)
- One command, bidirectional, infinite toggles

### 🔄 Thumbnail ↔ Video URL Swap
- Cursor on any line → command → URL type swaps
- `https://youtu.be/ID` ↔ `https://img.youtube.com/vi/ID/maxresdefault.jpg`

### 📋 Metadata Fetcher
- One command scaffolds the entire note: H1 title, thumbnail, description, ytaudio player, channel + watch links
- Fetches via YouTube oEmbed (no API key) + raw HTML scrape for description
- Strips tracking params; skips re-injection if content already exists

### ⏱ Live Timestamp Conversion
- `Timestamp: true` in frontmatter → `1:23` auto-links to `?t=1m23s` on save
- `hh:mm:ss` format supported: `1:02:30` → `?t=62m30s`
- Also available as a manual command

### 🎵 Sonicfonia — Background Audio
- Plays YouTube audio silently in a hidden 1×1px iframe
- Starts automatically on note switch when `Sonicfonia: true`
- Playlist support via YouTube Data API v3 (single videos need no key)
- Shuffle, repeat, queue navigation; status bar shows state + queue position

### 🎚 YT Audio Player — `ytaudio` block
- Interactive audio player from a code block in Reading View
- Play/pause, seekable gradient progress bar, live stream detection (`🔴 LIVE`)
- Edit button (⚙️) jumps cursor back to the block

---

## Installation

> Not yet in the Obsidian community store. Install manually.

1. Download the [latest release](https://github.com/sujit-waghmare/yt-supercut/releases/latest) — `main.js`, `styles.css`, `manifest.json`
2. In your vault: navigate to `.obsidian/plugins/` (enable hidden files if needed)
3. Create a folder named exactly `yt-supercut`
4. Drop all three files inside
5. Obsidian → Settings → Community Plugins → Refresh → Enable **YT Supercut**

---

## Usage

### Frontmatter properties

| Property | Type | Purpose |
|---|---|---|
| `YouTube Url` | string | Source URL — used by all features |
| `Thumbnail Url` | string | Written by metadata fetcher |
| `Channel UID` | string | Written by metadata fetcher |
| `Float Window` | boolean | `true` = show floating player |
| `Float X` | number | Horizontal position (1 unit = 100px). Default: 8 |
| `Float Y` | number | Vertical position (1 unit = 100px). Default: 1 |
| `iframe` | boolean | Reflects body state: `true` = iframe div present |
| `Sonicfonia` | boolean | `true` = play background audio on note switch |
| `Timestamp` | boolean | `true` = auto-convert timestamps on save |
| `Repeat` | boolean | Sonicfonia loop control |

### Init template

```yaml
---
YouTube Url: https://youtu.be/VIDEO_ID
Float Window: false
Float X: 8
Float Y: 1
Timestamp: true
Sonicfonia: false
iframe: false
---
```

### ytaudio block syntax

````markdown
```ytaudio
Title: My Video Title
YouTube Url: https://youtu.be/VIDEO_ID
```
````

---

## Float Window Position

The float window is positioned via two frontmatter properties. **1 unit = 100px** on both axes.

```
Screen coordinate grid (approximate):
┌─────────────────────────────────── X →
│  X:0  X:1  X:2  X:3  X:4  X:5  X:6  X:7  X:8
Y:0
Y:1                                        ← default
Y:2
Y:3
↓
```

**Examples:**

| Float X | Float Y | Result |
|---|---|---|
| 0 | 0 | Top-left corner |
| 8 | 1 | Top-right (default) |
| 3 | 3 | Centre-ish |
| 0 | 4 | Left side, middle |

Just edit the frontmatter numbers — the window repositions instantly because frontmatter changes trigger a plugin re-render.

```yaml
Float Window: true
Float X: 5
Float Y: 2
```

---

## Settings

Go to **Settings → YT Supercut**.

| Setting | Default | Description |
|---|---|---|
| Embed position | Below H1 | Where iframe injects when no existing element found |
| YouTube Data API key | — | Required for Sonicfonia playlist support only |
| Primary link (fallback) | — | Sonicfonia fallback when note has no `YouTube Url` |
| Enable Sonicfonia by default | ON | Auto-play without needing `Sonicfonia: true` per note |
| Shuffle playlist | OFF | Randomise playlist track order |
| Limit repeat count | OFF | Finite repeat vs loop forever |
| Bold title (ytaudio) | OFF | Bold title text in the audio player |
| Title / thumb / gradient colors | — | Style the ytaudio player |

### Getting a YouTube Data API key

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and sign in
2. **Select a project → New Project** → Create
3. **APIs & Services → Library** → search `YouTube Data API v3` → Enable
4. **APIs & Services → Credentials → + Create Credentials → API Key**
5. Copy the key (`AIza…`) → paste into Settings → YT Supercut → YouTube Data API key

### Setting a playlist as Sonicfonia fallback

1. Open any YouTube playlist → copy the URL: `https://youtube.com/playlist?list=PLxxxxxx`
2. Paste into **Settings → Primary link (fallback)**
3. Add your API key (required for playlist loading)
4. Enable **Enable Sonicfonia by default** or add `Sonicfonia: true` per note

---

## Commands

| Command | Description |
|---|---|
| `Toggle float window` | Shows/hides the floating player (writes to frontmatter) |
| `Toggle iframe embed` | Swaps `![Thumbnail]()` ↔ `<div class="youtube-container">` in note body |
| `Toggle: Thumbnail ↔ Video URL (cursor line)` | Swaps URL type on the current editor line |
| `Fetch YouTube metadata for current note` | Scaffolds the full note from the YouTube URL |
| `Convert (mm:ss)/(hh:mm:ss) to clickable timestamp links` | Manual timestamp conversion |
| `Sonicfonia: Toggle play/stop` | Starts or stops background audio |
| `Sonicfonia: Next track` | Advances playlist queue |
| `Sonicfonia: Previous track` | Reverses playlist queue |

---

## FAQ

**Do I need an API key?**  
No, for most features. Only required for Sonicfonia playlist support.

**Do Shorts work?**  
Yes. `/shorts/` URLs get a 9:16 portrait float window and a 9:16 body embed (`.youtube-container.shorts`).

**The float window doesn't show while editing.**  
Fixed in v1.2.0 — the window now shows in both editing (Live Preview) and Reading View.

**How do I move the float window?**  
Edit `Float X` and `Float Y` in frontmatter. 1 unit = 100px. The window repositions the moment you save.

**My `![Thumbnail]` image is rendering 1:1.**  
Fixed in v1.2.0 — CSS now forces `aspect-ratio: 16/9` on all `![Thumbnail]` images. Shorts thumbnails wrapped in `<div class="yts-thumb-shorts">` get 9:16.

**What does `iframe: true` in frontmatter mean?**  
It reflects the note body state. `true` = a `<div class="youtube-container">` embed is present; `false` = a static `![Thumbnail]()` is there. Updated automatically by the toggle command.

**How do I stop Sonicfonia?**  
`Sonicfonia: Toggle play/stop` — stops if playing, starts if stopped.

---

## Support

If this plugin saves you time, consider buying me a coffee:

**[paypal.me/waghmaresujit](https://paypal.me/waghmaresujit)**

---

<div align="center">

Made by [Waghmare](https://github.com/sujit-waghmare) · Obsidian Plugin

</div>
