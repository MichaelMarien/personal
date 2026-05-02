# Prinsessen Spelletjes 👑

Een offline, single-file web-app met vier mini-games voor een 5-jarige prinsessenfan die Nederlands spreekt. Gemaakt voor iPad in landschap-modus.

## Mini-games

- 🎴 **Memory** — vind 12 paren prinsessen (6×4 grid)
- 👗 **Aankleden** — sleep jurken, kronen, schoenen, accessoires op een prinses
- 🔍 **Zoeken** — vind verborgen items in de kasteelzaal, tuin, of kleerkamer
- ⚓ **Zeeslag** — prinsessen-themed battleship tegen de computer

## Live (GitHub Pages)

Apps in this repo, hosted via GitHub Pages:

- 👑 **Prinsessen Spelletjes:** https://michaelmarien.github.io/personal/princess/
- 🏀 **Scoreboard:** https://michaelmarien.github.io/personal/scoreboard/

> Enable Pages once in `Settings → Pages → Source: deploy from branch → main / (root)`. After that, every push to `main` redeploys automatically (~1 min).

## Spelen

### Op je laptop
```bash
open /Users/work/code/personal/princess/index.html
```
of in een browser: `file:///Users/work/code/personal/princess/index.html`

### Op de iPad
1. AirDrop `index.html` naar de iPad → opent in Safari.
2. Druk op de **deel-knop** → **"Zet op beginscherm"** (Add to Home Screen) → de app start full-screen met een 👑 icoon.

Of, voor een lokaal IP-adres:
```bash
cd /Users/work/code/personal/princess
python3 -m http.server 8080
```
Dan op de iPad in dezelfde wifi: `http://<je-mac-ip>:8080/`

## Tech

- Vanilla HTML / CSS / JS in één bestand
- Web Speech API (`nl-NL`) voor Nederlandse spraak
- Web Audio API voor gegenereerde geluiden (geen audio-bestanden)
- Inline SVG-art voor alle prinsessen en items
- Wake Lock API houdt het scherm aan tijdens het spelen
- Geen build-step, geen framework, geen netwerk

## Documenten

- [Design spec](docs/superpowers/specs/2026-05-02-princess-game-design.md)
- [Implementation plan](docs/superpowers/plans/2026-05-02-princess-game.md)
