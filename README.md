# CITY HEAT — GenosDB edition

A top-down, GTA1/2-style crime game that runs entirely in a **single HTML file** — and is now **peer-to-peer multiplayer with no server**, powered by [GenosDB](https://github.com/estebanrfp/gdb).

**▶ Play: https://estebanrfp.github.io/city-heat-genosdb/**

Open it in two browsers (or share a `#room` link and pick the same city) and you're in the same city together.

## What it is

Pure Canvas2D, zero external assets — the whole world (streets, buildings, cars, weather) is drawn procedurally, so there's nothing to fetch and it runs offline. Three cities (Curitiba, Chicago, Dubai), driving physics, weapons (fists / pistol / grenades), a wanted system, cops and pedestrians.

The original is single-player. **This edition adds serverless P2P multiplayer with GenosDB.**

## The GenosDB integration

Multiplayer is added with GenosDB's built-in P2P module (GenosRTC), reached via `db.room` after a one-line init:

```js
import { gdb } from "https://cdn.jsdelivr.net/npm/genosdb@latest/dist/index.min.js"
const db = await gdb('ch-' + city + '-' + room, { rtc: true })
```

No backend, no matchmaking service, no relays to run — GenosRTC handles the WebRTC transport and the signaling (over Nostr relays). What travels over the wire:

- **Player presence** — each player broadcasts its transform (position, aim, vehicle, weapon, HP, name) at ~15 Hz over an **ephemeral data channel** (`db.room.channel('p')`). Ephemeral channels are the right tool for high-frequency state — not the persistent graph (`db.put`).
- **Aim** — the exact direction each player is pointing / shooting.
- **Shots** — discrete fire events (`channel('f')`): bullets, grenades and melee are rendered on every peer from the real origin and angle.
- **PvP damage** — **self-authoritative**: each client computes hits against *its own* player and applies its own damage. Nobody decides your health but you → no desync, and cheat-resistant.
- **Kills & scoreboard** — on death the victim broadcasts credit to the killer (`channel('k')`); everyone updates the ⚔ scoreboard and the kill feed.

The **map is deterministic** (seeded per city), so nothing about the world needs to be synced — every player generates the identical city locally from the city name alone. That leaves only the players themselves to sync, which is a handful of lines against `db.room`.

The point: the entire real-time layer is tiny, and **there is no server anywhere** — browsers talk directly to browsers.

## Controls

- **Move / drive:** WASD / Arrows / left stick
- **Aim:** mouse (or drag on mobile)
- **Fire:** click / red button · **1‑2‑3** switch weapon
- **Enter / exit car:** F / green button · **Space** handbrake

## Multiplayer

The right-side panel lists every connected player with their vehicle (🚗 / 🚶), HP and kills. Share your URL with a `#roomname` and have friends pick the same city to join you.

## Roadmap

- **Role selection** — join as a cop, a truck, a sports car or a pedestrian, so the meaningful vehicles are players (dissolves NPC-traffic sync entirely, and makes cop-vs-robber chases real).

## Author

Esteban Fuster Pozzi (@estebanrfp) - Full Stack JavaScript Developer

## Credits

Built to showcase [**GenosDB**](https://github.com/estebanrfp/gdb) — a P2P graph database with real-time sync, offline-first storage and built-in WebRTC (GenosRTC). Original game concept: CITY HEAT.
