# Prinsessen Spelletjes — Design Spec

**Date:** 2026-05-02
**Audience:** A 5-year-old Dutch-speaking girl who loves princesses
**Target device:** iPad Safari, landscape orientation

## Goal

A self-contained, offline-capable web app with four princess-themed mini-games she can navigate independently. Friendly Dutch labels, audio cheers, big tap targets, no failure states that feel punishing.

## Architecture

- **Single-file HTML app** (matches the `scoreboard/` pattern in the parent repo)
- One `index.html` containing embedded CSS, JS, and inline SVG art
- No build step, no framework, no external assets, no network calls
- Audio: Web Speech API (`nl-NL`) for TTS; Web Audio API for generated tones
- Persistence: none (no scores or progress saved)
- Wake lock used to keep the screen awake during play

## Top-level structure

A home menu with four large tappable tiles, one per mini-game:

| Tile | Label (NL) | Icon |
| --- | --- | --- |
| Memory | Memory | 🎴 |
| Dress-up | Aankleden | 👗 |
| Find item | Zoeken | 🔍 |
| Battleship | Zeeslag | ⚓ |

Each mini-game is a separate DOM section in the same page; switching just shows/hides sections (no routing, no URL changes). Each mini-game has a "← terug" button returning to the menu, and where applicable a "Nog een keer" button to replay.

A persistent **mute toggle** (🔇/🔊) lives in the top-right corner across all screens.

## Mini-game 1 — Memory (🎴)

- 6×4 grid → 24 cards → 12 pairs
- Card back: pink with a gold crown
- Card faces: 12 princess portraits, each with a signature color and emoji
  - Elsa ❄️ (ijsblauw), Belle 🌹 (geel), Ariel 🐚 (groen), Rapunzel 💜 (paars), Aurora 🌸 (roze), Sneeuwwitje 🍎 (rood), Cinderella ✨ (lichtblauw), Jasmine 🪔 (turquoise), Mulan 🐉 (rood-goud), Tiana 🐸 (groen-goud), Moana 🌺 (oceaanblauw), Pocahontas 🍃 (terracotta)
- Cards shuffled with Fisher-Yates per game
- Tap to flip; if 2 flipped match → stay revealed, sparkle, chime, randomized Dutch cheer ("Goed zo!", "Super!", "Wat knap!", "Geweldig!", "Heel goed!"). If not match → both flip back after ~1.2s.
- Win: all pairs found → "Gewonnen! 🎉" overlay + confetti + TTS "Je hebt gewonnen!" + "Nog een keer" reshuffles, "← terug" to menu

## Mini-game 2 — Aankleden (👗)

- Princess silhouette in center; palette of items along the right edge
- Princess selector row at top: 3-4 silhouettes (different hair/skin tone, same outfit slots)
- Drag-and-drop using pointer events; items snap to slots
- 4 categories, 4 options each:
  - **Jurken** (dresses): pink, blue, yellow, purple
  - **Kronen** (crowns): gold, silver, flower-crown, tiara
  - **Schoenen** (shoes): glass slippers, pink heels, red shoes, ballet flats
  - **Accessoires**: wand 🪄, necklace, bouquet, butterfly
- Dropping a new item in a slot replaces the previous one
- Tap an equipped item to remove it
- Each equip → sparkle + chime
- "📷 Klaar!" button shows a celebratory "ta-da!" overlay; "Nog een keer" resets

## Mini-game 3 — Zoeken (🔍)

- 3 scenes she can pick or cycle through:
  - 🏰 **Kasteelzaal** (throne room)
  - 🌳 **Tuin** (castle garden)
  - 👗 **Kleerkamer** (wardrobe)
- Each scene: ~15-20 small SVG items scattered, including distractors (e.g. crowns of different colors)
- Target shown at the top: "Vind de gouden kroon 👑"
- Tap correct item → sparkle, chime, "Goed gevonden!", target swaps to a new one
- Tap wrong → gentle wiggle, soft "Hmm... probeer nog eens"
- Find 5 items per round → "Klaar! 🎉" → choose another scene or back to menu
- Targets rotate from an 8-10 item pool per scene (not the same 5 every time)
- Hint affordance: after 15s without progress, a 💡 button appears that pulses the correct item

## Mini-game 4 — Zeeslag (⚓)

Classic battleship rules, princess-themed.

- Two 8×8 grids stacked vertically (landscape iPad)
  - Top: "Hun koninkrijk" (computer's hidden grid — she taps to attack)
  - Bottom: "Jouw koninkrijk" (her own fleet, visible, shows incoming attacks)
- Fleet (5 pieces per side):
  - 🛼 Koets (carriage, 5)
  - 🦢 Zwanenboot (swan boat, 4)
  - 🗼 Toren (tower, 3)
  - 🦄 Eenhoorn (unicorn, 3)
  - 💎 Juweel (jewel, 2)
- Placement phase:
  - Default: **"Plaats voor mij!"** auto-places her fleet randomly (the expected path for a 5yo)
  - Manual: tap piece → tap-and-hold to rotate → drag to place
- Play phase:
  - She taps a square on the enemy grid: ✨ hit + sparkle + chime + "Raak!"; or 💧 miss + splash + "Mis..."
  - Computer attacks after short delay using random-only AI (no hunting/targeting — keeps games winnable)
  - Fully sunk piece reveals its icon with "Gezonken!" sparkle
- End:
  - Win → "Je hebt gewonnen! 🎉" + confetti + TTS cheer
  - Lose → "Bijna! Nog een keer?" — never says "you lost"; offers a fresh round
- "← terug" returns to menu, "Nog een keer" replays

## Cross-cutting

### Audio

- TTS via `speechSynthesis`, `lang: 'nl-NL'`; silent fallback if voice unavailable
- Cheer pool: "Goed zo!", "Super!", "Wat knap!", "Geweldig!", "Heel goed!" — randomized
- SFX generated via Web Audio API: sparkle chime (ascending arpeggio), splash (noise burst), success fanfare — no audio files
- Mute toggle in top-right; in-memory state (resets on reload)

### Visual style

- Pastel palette: blush pink `#FFD9E5`, lavender `#E8D5FF`, mint `#D5F5E8`, gold accent `#FFD700`
- Rounded everything (16-24px radii), soft shadows, CSS sparkle particles on success
- Princess art: stylized inline SVG (circles for heads, flowing dresses, no realistic faces) — friendly and abstract
- Min tap target 80×80px
- Landscape lock via CSS; "draai je iPad" overlay if held in portrait

### Behavior

- Wake lock requested on game start (matches `scoreboard/` pattern)
- Respects `prefers-reduced-motion`
- All animations CSS-only — no canvas

### Error handling

- No network, no users, no data — minimal error surface
- TTS unavailable → silent (cheers still appear as text overlays)
- All inputs are pointer events on bounded UI — no user text input

### Testing

- Manual testing on iPad Safari (the real target)
- Per mini-game checklist: win/end condition, mute toggle, back navigation, orientation lock, drag/tap interactions
- No automated tests — visual/audio behavior is what matters and is hard to assert in JS

## File layout

```
princess/
  index.html              # the entire app
  docs/superpowers/specs/
    2026-05-02-princess-game-design.md   # this file
```

## Out of scope

- Score tracking or progress saving
- Multiplayer / network
- Localization beyond Dutch
- Accessibility beyond reduced-motion (target user is a single 5yo)
- Build step or framework
