# Deck of Projects

An interactive portfolio index built as a playing card deck. Each card represents a project — hover to lift, click to draw, drag to flip, or shuffle for a random pick.

**Live:** https://tammyzhou23.github.io/deck-of-projects

## Interactions

- **Hover** — card lifts from the fan
- **Click** — draws the card face-up with project details; stacked cards flip to show their colored backs
- **Drag** — rotate the active card 360° to reveal the back
- **Draw a card** — shuffle animation gathers the deck, then fans out with a random card drawn

## Stack

Single self-contained `index.html` — no build tools, no frameworks, no dependencies. Embedded CSS and JS only.

- Typography: [Young Serif](https://fonts.google.com/specimen/Young+Serif) + [Figtree](https://fonts.google.com/specimen/Figtree) via Google Fonts
- Colors: OKLCH throughout, 1970s retro rainbow palette
- 3D card flip: CSS `transform-style: preserve-3d` + `backface-visibility: hidden`

## Local development

```bash
npx serve -l 3847
# open http://localhost:3847
```
