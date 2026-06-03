# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Static single-page wedding invitation website for 邱鈺皓 & 陳映安 (December 26, 2026). Deployed to GitHub Pages at `https://yanchen0902.github.io/wedding/`.

No build step. Open `index.html` directly in a browser or serve with any static file server:
```bash
cd /Users/annychen/Documents/claude101/婚禮網站
python3 -m http.server 8080
```

## File structure

- `index.html` — deployed version (served by GitHub Pages from repo root)
- `assets/` — all photo assets referenced by `index.html`
- `site/` — working copy (mirrors root; keep in sync when making changes)

When editing, update **both** `index.html` and `site/index.html`, then commit and push to deploy.

## Architecture

The entire app lives in one `index.html` with no bundler. It uses:
- **React 18** + **ReactDOM** loaded from unpkg CDN (UMD builds)
- **Babel standalone** for in-browser JSX transpilation
- Three `<script type="text/babel">` blocks that share state by assigning to `window`

### Script block responsibilities

1. **Block 1** — shared components and data: `Snowfall`, `Reveal`, `ScheduleList`, `DressDetails`, gallery components (`WeddingPortraits`, etc.), `RSVPForm`, `RsvpStatus`, `InviteFooter`, schedule/FAQ data
2. **Block 2** — `FloatingRsvpEnvelope` (the animated RSVP envelope widget, portaled to `document.body`)
3. **Block 3** — `SnowDiaryEditorial`, `SnowDiaryCarousel`, `MagazineBody`, three hero variations (`HeroCenterPlate`, `HeroLowLeft`, `HeroSplitHorizon`)
4. **Block 4** — entry point: renders `<VariationMagCenterPlate texture="tex-paper" snowLayout="carousel" />`

### Hero / layout variations

Three hero variants exist but only one renders at a time (controlled in Block 4):
- `VariationMagCenterPlate` — cinematic full-bleed photo + centered frosted-glass plate (currently active)
- `VariationMagLowLeft` — editorial magazine cover, title bottom-left
- `VariationMagSplitHorizon` — dark background, names spanning full width

All three share `MagazineBody` for the sections below the hero (Schedule, Wardrobe, Snow Diary, Portraits, RSVP closer, Footer).

### Image paths

Images are referenced as `assets/<filename>` relative to `index.html`. The `window.IMG` helper (defined inline) is a no-op pass-through — it exists so the design tool and the static site use the same JSX code.

### RSVP state

Currently stored in `localStorage` only — guests' responses are not sent anywhere. Airtable integration is planned but not yet implemented.

### Responsive breakpoints

Defined in the inline `<style>` block:
- `≤ 900px` — tablet: single-column editorial blocks, smaller headings
- `≤ 640px` — mobile: hero reflows, schedule grid compacts, snow diary stacks
- `≤ 380px` — very small phones: hero plate padding trimmed