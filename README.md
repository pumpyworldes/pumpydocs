# Pumpy World Documentation

Official documentation for [Pumpy World](https://pumpyworld.space) — an infinite procedurally generated 3D open world you can explore in your browser, alone or with others online.

**Play:** [pumpyworld.space](https://pumpyworld.space) · **Community:** [@pumpyworld](https://x.com/pumpyworld)

---

## Table of contents

- [Overview](#overview)
- [Getting started](#getting-started)
- [Controls](#controls)
- [Multiplayer](#multiplayer)
- [URL parameters](#url-parameters)
- [System requirements](#system-requirements)
- [Development](#development)
- [Deployment](#deployment)
- [Architecture](#architecture)
- [Credits](#credits)

---

## Overview

Pumpy World is a browser-based 3D experience built with **Three.js** and **WebGL**. The terrain is generated procedurally using Simplex noise — every hill, ridge, and stretch of grass is created on the fly as you move. There is no download or install: open the site, and you are in the world.

### What you can do

- Explore an endless rolling landscape of hills, grass, and sky
- Jump across terrain and switch between camera modes
- Meet other players in real time when multiplayer is enabled
- Toggle ambient music and fullscreen for an immersive session

### What Pumpy World is not

- **Not optimized for mobile** — portrait mode shows a warning. Use a desktop or laptop with a keyboard and mouse.
- **Not a traditional MMO** — multiplayer is a lightweight relay that syncs player positions; there are no inventories, quests, or chat (yet).

---

## Getting started

1. Open [pumpyworld.space](https://pumpyworld.space) in a modern desktop browser (Chrome, Firefox, Safari, or Edge).
2. Wait for the world to load — terrain chunks generate around you automatically.
3. Use **WASD** or arrow keys to move, **Space** to jump.
4. Press **P** to lock the mouse pointer for smoother camera control.
5. Click the music button on the right side panel to toggle ambient audio.

> **Tip:** Press **F** for fullscreen and **V** to switch between third-person and fly camera modes.

---

## Controls

| Key | Action |
|-----|--------|
| **W** / **↑** | Move forward |
| **S** / **↓** | Move backward |
| **A** / **←** | Strafe left |
| **D** / **→** | Strafe right |
| **Shift** | Sprint (boost speed) |
| **Space** | Jump |
| **Ctrl** / **C** | Crouch |
| **P** | Toggle pointer lock |
| **V** | Switch camera mode (third-person ↔ fly) |
| **F** | Toggle fullscreen |
| **B** | Toggle debug mode (`#debug` in URL) |

### Camera modes

- **Third-person** — default. The camera follows behind your character. Move the mouse (with pointer lock) to look around.
- **Fly** — free camera. Useful for exploring the landscape from above. Press **V** to switch back.

### UI buttons (right side panel)

| Button | Action |
|--------|--------|
| Music | Toggle ambient background music |
| X (Twitter) | Follow [@pumpyworld](https://x.com/pumpyworld) |
| Docs | Open this documentation on GitHub |
| Info (i) | In-game about panel with quick overview |

---

## Multiplayer

Pumpy World supports real-time multiplayer through a lightweight **WebSocket relay server**. When connected, you see other players as colored pills moving through the same procedural world.

### How it works

1. Each browser tab is one player.
2. Your position, rotation, and name are sent to the relay server ~20 times per second.
3. The server broadcasts a snapshot of all connected players to everyone.
4. Other players appear with their assigned colors and name tags.

### Default connection

By default, the client tries to connect to `ws://<same-host>:8080`. On production (pumpyworld.space), multiplayer requires a separate relay server unless one is deployed.

### Custom server URL

Point clients at your own relay with a URL parameter:

```
https://pumpyworld.space/?server=wss://your-relay.example.com
```

For local LAN play:

```
http://localhost:5173/?server=ws://192.168.1.100:8080
```

### Set your player name

```
https://pumpyworld.space/?name=YourName
```

Names are limited to 24 characters. If omitted, a random tag like `Player-4821` is assigned.

### Running the relay server

```bash
cd server
npm install
npm start
```

The server listens on port **8080** by default. Set `PORT` environment variable to change it.

See [docs/multiplayer.md](./docs/multiplayer.md) for protocol details and hosting tips.

---

## URL parameters

| Parameter | Example | Description |
|-----------|---------|-------------|
| `server` | `?server=wss://relay.example.com` | WebSocket relay URL for multiplayer |
| `name` | `?name=Pumpy` | Your display name (max 24 chars) |

Combine parameters:

```
https://pumpyworld.space/?name=Pumpy&server=wss://relay.example.com
```

### Debug mode

Append `#debug` to the URL or press **B** in-game to enable debug overlays (stats, terrain wireframes, etc.).

```
https://pumpyworld.space/#debug
```

---

## System requirements

### Recommended

- **OS:** Windows 10+, macOS 12+, or Linux
- **Browser:** Latest Chrome, Firefox, Safari, or Edge
- **GPU:** Dedicated graphics or modern integrated GPU with WebGL 2 support
- **Input:** Keyboard + mouse
- **Network:** Broadband connection (for multiplayer)

### Not supported

- Mobile phones and tablets (portrait orientation shows a warning)
- Browsers without WebGL support
- Internet Explorer

---

## Development

### Prerequisites

- [Node.js](https://nodejs.org/) 18+
- npm

### Local setup

```bash
git clone https://github.com/rapkuryer/pumpy.git
cd pumpy
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173).

### Project structure

```
├── index.html          # Entry page & UI shell
├── sources/
│   ├── index.js        # Bootstrap
│   ├── style.css       # UI styles
│   └── Game/
│       ├── Game.js     # Main game loop
│       ├── State/      # Logic: player, terrain, network, controls
│       └── View/       # Rendering: materials, shaders, meshes
├── public/             # Static assets (audio, textures)
├── server/             # WebSocket multiplayer relay
└── dist/               # Production build output
```

### Build for production

```bash
npm run build
```

Output goes to `dist/`.

See [docs/development.md](./docs/development.md) for architecture and shader details.

---

## Deployment

Pumpy World is deployed on **Vercel** with custom domains `pumpyworld.space` and `www.pumpyworld.space`.

### Deploy steps

1. Import the repository into Vercel
2. Framework preset: **Vite**
3. Build command: `npm run build`
4. Output directory: `dist`
5. Add custom domains and point DNS to Vercel

```bash
vercel --prod
```

See [docs/deployment.md](./docs/deployment.md) for DNS and multiplayer relay hosting.

---

## Architecture

### Tech stack

| Layer | Technology |
|-------|------------|
| Rendering | Three.js, WebGL, GLSL shaders |
| Build | Vite, vite-plugin-glsl |
| Terrain | Simplex noise, Web Workers |
| Multiplayer | WebSocket relay (`ws` package) |
| Math | gl-matrix |

### Procedural terrain

- The world is divided into **chunks** that generate around the player.
- Height maps are computed with Simplex noise in **Web Workers** to keep the main thread responsive.
- Grass, fog, sun shading, and sky are handled by custom GLSL shaders.

### Multiplayer relay

A minimal Node.js server (`server/server.js`) that:

1. Assigns each client a unique ID on connect
2. Receives position updates from clients
3. Broadcasts a world snapshot to all clients every 50ms

No game logic runs on the server — it is a pure relay.

---

## Credits

Pumpy World is based on [Bruno Simon's Infinite World](https://github.com/brunosimon/infinite-world) open-source demo, extended for the Pumpy World community.

Built with love for explorers, jumpers, and anyone who wants to get lost in an infinite green world.

**Follow us:** [@pumpyworld](https://x.com/pumpyworld)
