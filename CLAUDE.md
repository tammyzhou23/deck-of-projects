# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A single-page portfolio card deck — interactive playing-card spread with flip, drag-to-rotate, and shuffle interactions. No build tools, no frameworks — one self-contained `index.html` with embedded CSS and JS.

## Running Locally

```bash
npx serve -l 3847
# then: open http://localhost:3847
```

## Architecture

Everything lives in `index.html`. The file is structured: CSS variables → layout CSS → card CSS → animations → responsive → HTML → JS.

### Design Tokens

- **Colors use OKLCH** — not hex or HSL. 6 card palettes (`--card-1` through `--card-6`) in a 1970s retro rainbow progression (red-orange → warm orange → amber → teal → sky blue → deep navy). Surface tokens tinted warm (`--surface-bg: oklch(0.975 0.006 75)`).
- **Typography**: Young Serif (display/headings) + Figtree (body), loaded from Google Fonts.
- **Spacing scale**: 4px steps (`--space-xs` through `--space-2xl`).

### Card Structure

Each card is a 3D flip unit:

```
.card-wrapper.card-N          ← absolutely positioned, fan transform applied here
  .card-inner                 ← preserve-3d, flip on Y-axis
    .card-face.card-front     ← white face, monoline SVG illustration + corner indices
    .card-face.card-back      ← colored face, inline SVG generative pattern
```

- `.card-inner.is-flipped` → `rotateY(180deg)` shows the back
- Both faces use `backface-visibility: hidden`
- **CRITICAL**: `body { overflow-x: hidden }` or any overflow on an ancestor flattens `preserve-3d`. Do not add overflow rules to body or `.stage`.

### Fan Layout

Cards are absolutely positioned from `left: 50%; top: 50%` (top-left corner at spread center). The CSS fan uses `translate(-50%, -50%) translate(Xpx, Ypx) rotate(Rdeg)` — the first translate centers the card on the origin, the second applies the fan offset.

Fan positions are duplicated in three places — keep them in sync:
1. CSS `.card-wrapper:nth-child(N)` default transforms
2. CSS `.card-spread:not(.has-active) .card-wrapper:nth-child(N):hover` transforms
3. JS `defaultPositions` array

Hover rules are scoped to `.card-spread:not(.has-active)` so they don't interfere in expanded/stacked state.

### JS State Machine

Three states managed in the IIFE at bottom of `<body>`:

| State | Description | Classes |
|---|---|---|
| **Fanned** | Default fan spread, all fronts showing | none |
| **Expanded** | One card front-facing enlarged, others stacked + flipped | `.is-active`, `.is-stacked`, `.has-active` |
| **Shuffling** | Gather → jitter → fan animation | `isShuffling` flag |

Key functions:
- `setFanned()` — removes all inline transforms after a 520ms timeout so CSS `:hover` rules resume
- `setExpanded(index)` — active card gets `translate(-150px, -165px) rotate(0deg)`; stacked cards get `is-flipped` + compact stack layout. Stacked center offset: `startX = -((total-1)*spacing)/2 - 114` (compensates for `left: 50%` offset where 114 = half of 228px card width)
- `shuffle()` — async: gather to center (400ms) → jitter keyframe (800ms) → fan out with random active card

### Drag-to-Rotate (Expanded State)

When a card is expanded, mousedown/touchstart initiates a 2-axis trackball drag:
- Horizontal drag → `rotateY` (full 360°)
- Vertical drag → `rotateX` (clamped ±80°)
- Resistance increases with distance from drag origin: `r = 1 / (1 + dist / 80)`
- On release: snaps `rotateY` to nearest 180°, clears inline transform and sets `is-flipped` to match face shown

### Tuck Box

A photo (`tuck-box.png`) with transparent background, displayed below the card spread. No blend mode needed. Enters with a slide-up animation (`box-enter` keyframe, 1s, 0.7s delay).

### Responsive

CSS `transform: scale()` on `.card-spread` at three breakpoints (1100px, 820px, 580px). The spread has a fixed pixel height that shrinks to match.
