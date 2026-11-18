# Untranslatable

A bluffing party game for 3 to 20 players about real words that have no neat English translation.

**Play:** https://untranslatable.apps.automaton.monster/

## How to play

1. A real word appears, such as *Kummerspeck* (German).
2. Everyone secretly writes a fake meaning that sounds convincing.
3. All fakes are shuffled with the real meaning, and everyone votes for the one they believe.
4. Finding the real meaning scores **+2**. Every colleague who falls for your fake gives you **+1**.

A game of 5 words takes about 10 minutes. The host creates a room (optionally with a password), shares the link, and controls when the next word starts.

## How it works

- `server.js`: a dependency-free Node.js HTTP server that serves the frontend and the JSON game API.
- `game.js`: rooms, players, phases, and scoring. Rooms live in memory.
- `words.js`: the curated word bank.
- `farewell.js`: a farewell message that greets everyone entering a room on one specific day, and never appears on any other day.
- `public/`: the plain HTML, CSS, and JavaScript frontend. Browsers poll the room state once a second, because Automaton's runtime routing does not support WebSockets.

## Run it on its own

It is a plain Node.js server and does not need Automaton. Run it from a copy of this folder (`server.js`, `game.js`, `words.js`, `farewell.js` and `public/`):

```yaml
services:
  untranslatable:
    image: node:22-bookworm-slim
    user: "1000:1000"
    working_dir: /app
    command: node server.js
    ports: ["8080:8080"]
    volumes:
      - ./:/app:ro
```

Environment variables (none are declared in `automaton.json`):

| Variable | What it does |
| --- | --- |
| `AUTOMATON_PORT` | Port to listen on (default 8080; `PORT` also works). Change the `ports:` mapping to match. |
| `FAREWELL_DATES` | Optional. Comma-separated dates (`YYYY-MM-DD`) on which the farewell message shows (default `2026-10-29,2026-10-30`). |

No volume is needed: rooms live in memory, so a restart ends the games in progress. Put it behind HTTPS if it is reachable from the internet.

## Run locally

```bash
node server.js
```

Then open http://localhost:8080. Set `AUTOMATON_PORT` to use another port.

## Deploy

```bash
automaton validate
automaton deploy
```

The deployment contract is in `automaton.json`.
