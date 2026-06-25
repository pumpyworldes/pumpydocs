# Development Guide

Technical guide for contributors and developers working on Pumpy World.

## Prerequisites

- Node.js 18+
- npm
- A WebGL-capable browser
- (Optional) A second machine or browser tab for multiplayer testing

## Setup

```bash
git clone https://github.com/rapkuryer/pumpy.git
cd pumpy
npm install
npm run dev
```

Vite dev server runs at `http://localhost:5173` with hot reload.

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start Vite dev server |
| `npm run build` | Production build to `dist/` |

## Architecture

### Game loop

```
Game.js
  ├── State (logic)
  │     ├── Player      — movement, jump, position
  │     ├── Camera      — third-person / fly modes
  │     ├── Controls    — keyboard & pointer input
  │     ├── Chunks      — terrain chunk management
  │     ├── Terrains    — height field computation
  │     ├── Network     — WebSocket multiplayer client
  │     ├── DayCycle    — sun position & lighting
  │     ├── AmbientMusic— background audio
  │     └── Time        — delta time
  └── View (rendering)
        ├── Renderer    — Three.js WebGL renderer
        ├── Terrains    — mesh instances per chunk
        └── Materials   — GLSL shader materials
```

### Path aliases

Vite resolves `@/` to `sources/`:

```js
import Game from '@/Game.js'
import State from '@/State/State.js'
```

### Terrain generation

1. `Chunks` tracks which chunks exist around the player
2. Each `Chunk` requests height data from a **Web Worker**
3. Worker uses **Simplex noise** to compute elevation
4. `Terrain` builds geometry; `TerrainMaterial` shades it with GLSL

### Shaders

Custom shaders live in `sources/Game/View/Materials/shaders/`:

| Shader | Purpose |
|--------|---------|
| `terrain/` | Ground color, sun shade, fog |
| `grass/` | Instanced grass blades with wind |
| `skySphere/` | Sky gradient |
| `skyBackground/` | Distant sky backdrop |
| `stars/` | Night stars |
| `player/` | Character pill shading |
| `noises/` | Noise texture generation |

Partial GLSL includes are in `shaders/partials/`.

### Multiplayer client

`Network.js` handles:

- WebSocket connect / reconnect
- Player name from `?name=` param
- Transform broadcast every 50ms
- Remote player map for the view layer

### UI events

The HTML shell communicates with the game via custom events:

```js
// Music toggle (from button click)
window.dispatchEvent(new CustomEvent('pumpy:music-toggle'))

// Music state sync (from game to button)
window.dispatchEvent(new CustomEvent('pumpy:music-state', { detail: { enabled: true } }))
```

## Debug mode

Add `#debug` to the URL or press **B**:

- Enables lil-gui panels
- Shows stats.js FPS counter
- Exposes terrain and rendering tweaks

## Building

```bash
npm run build
```

Preview the build:

```bash
npx vite preview
```

## Code style

- ES modules (`import` / `export`)
- Classes for game systems
- gl-matrix for vector math
- EventsEmitter for input events

## Based on

This project extends [Bruno Simon's Infinite World](https://github.com/brunosimon/infinite-world). Key additions:

- Multiplayer relay client & server
- Jump mechanics with custom gravity
- Ambient music system
- UI overlay (music, social, info panel)
- Vercel deployment config
