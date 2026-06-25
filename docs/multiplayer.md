# Multiplayer Guide

How real-time multiplayer works in Pumpy World and how to host your own session.

## Overview

Multiplayer in Pumpy World is intentionally simple:

- No accounts or login
- No chat or voice (browser only syncs positions)
- Each tab = one player (colored pill in the world)
- Server is a **relay** — it does not run game logic

## Quick start (LAN)

### 1. Start the relay server

On the host machine:

```bash
cd server
npm install
npm start
```

You should see:

```
Infinite World multiplayer server running on ws://localhost:8080
```

### 2. Find your local IP

```bash
# macOS / Linux
ipconfig getifaddr en0
# or
hostname -I
```

Example: `192.168.1.42`

### 3. Share the link

Other players on the same network open:

```
http://<host-ip>:5173/?server=ws://<host-ip>:8080&name=Alice
```

For production:

```
https://pumpyworld.space/?server=ws://<host-ip>:8080&name=Alice
```

> **Note:** Browsers block mixed content (HTTPS page → WS relay). For public play over HTTPS, deploy the relay with WSS (TLS) or use a tunnel like ngrok.

## URL parameters

| Param | Required | Description |
|-------|----------|-------------|
| `server` | Yes (for custom relay) | WebSocket URL, e.g. `ws://host:8080` or `wss://relay.example.com` |
| `name` | No | Display name, max 24 characters |

## Connection logic

The client resolves the server URL in this order:

1. `?server=` query parameter
2. `ws://<current-hostname>:8080` (default)

If the connection drops, the client retries every 2 seconds.

## Protocol

All messages are JSON over WebSocket.

### Server → Client

**Welcome** (on connect):

```json
{ "type": "welcome", "id": 1 }
```

**Players snapshot** (every 50ms):

```json
{
  "type": "players",
  "players": [
    { "id": 1, "x": 10, "y": 0, "z": 5, "rotation": 1.57, "color": "#4ade80", "name": "Alice" },
    { "id": 2, "x": 20, "y": 0, "z": 12, "rotation": 0, "color": "#43b65f", "name": "Bob" }
  ]
}
```

### Client → Server

**State update** (~20/sec):

```json
{
  "type": "state",
  "x": 10,
  "y": 0,
  "z": 5,
  "rotation": 1.57,
  "color": "#4ade80",
  "name": "Alice"
}
```

## Hosting in production

### Option A: Separate VPS

1. Deploy `server/` to a VPS (Fly.io, Railway, Hetzner, etc.)
2. Expose port 8080 (or set `PORT` env var)
3. Use WSS for HTTPS sites:

```
https://pumpyworld.space/?server=wss://relay.yourdomain.com
```

### Option B: ngrok (quick testing)

```bash
cd server && npm start
# In another terminal:
ngrok http 8080
```

Share:

```
https://pumpyworld.space/?server=wss://<ngrok-id>.ngrok-free.app
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No other players visible | Check relay is running and `?server=` URL is correct |
| Connection fails on HTTPS | Use `wss://` not `ws://` |
| Players flicker | Normal on high latency; relay broadcasts at 20 Hz |
| Firewall blocks | Open port 8080 on host machine |
