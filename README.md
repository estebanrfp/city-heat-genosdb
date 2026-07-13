# CITY HEAT — GenosDB edition

A top-down, GTA1/2-style crime game in a **single HTML file**, with a full **serverless peer-to-peer multiplayer** mode — identity, security and governance included — powered by [GenosDB](https://github.com/estebanrfp/gdb).

**▶ Play: https://estebanrfp.github.io/city-heat-genosdb/**

Pure Canvas2D, zero external assets: the whole world (streets, buildings, cars, weather) is drawn procedurally, so there's nothing to fetch and it runs offline. Three cities (Curitiba, Chicago, Dubai), driving physics, weapons (fists / pistol / grenades), a wanted system.

## Two modes

At launch you pick one:

- **🎮 Single Player** — the classic game: AI **traffic, pedestrians and cops**, a wanted/heat system. No login, just play.
- **🌐 Multiplayer** — a **serverless P2P manhunt**. Sign in with a cryptographic identity, then every meaningful vehicle on the street is a **real player**.

## Multiplayer: the manhunt

Instead of syncing dumb NPC traffic (which drifts across clients), **the vehicles that matter are players**. You pick a role and embody it:

- **🏃 Runner** — the outlaw. Survive and evade. Earns points while alive, more the more cops close in.
- **🚔 Cop / 🚚 Truck / 🏎️ Sport** — the hunters, each with distinct handling.

Cops corner the runner: **3 cops within range for 4 seconds = BUSTED** (a pulsing red "surrounded" warning builds up), and the capturing cops split the bounty. A live **leaderboard** ranks everyone by score, with an achievement **badge** next to each name.

Because there are no shared NPCs, a **late-joiner just spawns now** — no shared clock, no catch-up. Cop-vs-robber chases happen between real humans.

Matches run in **rounds** (2 min): the round ends when the timer runs out (**runners survive → they win**) or every runner is caught (**cops win**), and a results screen shows the winner and final scores before the next round. **Role slots** keep it balanced — the intro shows live counts (e.g. `COP 2/3`) and caps each role per match.

## What GenosDB powers

This game is a showcase of the full GenosDB stack — **there is no server anywhere**, browsers talk directly to browsers:

```js
import { gdb } from "https://cdn.jsdelivr.net/npm/genosdb@latest/dist/index.min.js"
const db = await gdb('city-heat-arena', {
  rtc: true,                                   // P2P (GenosRTC: WebRTC + Nostr signaling)
  sm: { superAdmins: [...], governanceRules }, // identity, RBAC, signed ops, governance
})
```

- **Real-time P2P (GenosRTC)** — player presence (position, aim, role, HP, name) at ~15 Hz, plus discrete shot and kill events, all over ephemeral data channels (`db.room.channel`). The right tool for high-frequency state — not the persistent graph.
- **Cryptographic identity (Security Manager)** — sign in with a **WebAuthn passkey** or a **BIP39 mnemonic**. A protected passkey gives a **perpetual session** (silent resume on return, no prompt).
- **Signed database writes** — identity and persisted progress (career points, the shared match state) are cryptographically **signed and verified by every peer** — impossible to forge.
- **Governance** — a merit ladder (`governanceRules`, last-match-wins): playing earns career points that promote your **achievement badge** (🥉→🥈→🥇), each promotion **signed by an online superadmin**. The badge is cosmetic reputation — it never gates who can play.
- **Reactive graph queries** — the city played (a shared `match` node) and the live roster of badges are read reactively with `db.get` / `db.map`.
- **Offline-first, no backend** — GenosRTC handles WebRTC transport and signaling; no matchmaking service, no relays to run.

The **map is deterministic** (seeded per city), so the world itself is never synced — every player generates the identical city locally. Only the players are synced.

### Honest scope

Sign-in identity and **persisted progress (career, badges, match state) are signed**; the **real-time in-match actions** (positions, shots, score) travel over ephemeral GenosRTC channels and are **self-authoritative**, not cryptographically signed. Governance promotions require an **online superadmin** running the engine (a demo one is one click away; in production a fallback server plays that role).

## Controls

- **On foot:** WASD / Arrows to move · the **mouse aims** your weapon.
- **Driving (desktop):** the **mouse steers** the car — point where you want to go and fire; **W / S** throttle & reverse · **Space** handbrake for drifts.
- **Touch / mobile:** the left stick both drives and steers.
- **Both:** **click / 🔥** fire · **1‑2‑3** switch weapon · **F / 🚪** enter or exit a car.

## Play together

Open **Multiplayer** in two browsers on the same city and you're in the same match. To watch governance promote players, click **🛡️ Superadmin (demo)** in one window — its engine promotes the others as they earn career points.

## Author

Esteban Fuster Pozzi (@estebanrfp) - Full Stack JavaScript Developer

## Credits

Built to showcase [**GenosDB**](https://github.com/estebanrfp/gdb) — a P2P graph database with real-time sync (GenosRTC/WebRTC), offline-first storage, cryptographic identity, RBAC and governance. Original game concept: CITY HEAT.
