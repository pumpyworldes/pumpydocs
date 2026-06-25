# Controls Reference

Complete keyboard and UI reference for Pumpy World.

## Movement

| Input | Action |
|-------|--------|
| W / Arrow Up | Forward |
| S / Arrow Down | Backward |
| A / Arrow Left | Strafe left |
| D / Arrow Right | Strafe right |
| Shift (hold) | Sprint — 3× base movement speed |

Movement direction follows the camera angle in third-person mode. Diagonal inputs (e.g. W + A) move at 45° angles.

## Actions

| Input | Action |
|-------|--------|
| Space | Jump (only when grounded, third-person mode) |
| Ctrl / C | Crouch |

Jump physics use custom gravity curves for a smooth takeoff and natural fall. You cannot jump in fly camera mode.

## Camera

| Input | Action |
|-------|--------|
| P | Toggle pointer lock |
| V | Switch between third-person and fly camera |
| Mouse (pointer locked) | Look around / steer fly camera |

### Third-person mode

The camera orbits behind your character. Your character rotates to face the movement direction.

### Fly mode

Free-flight camera — no gravity, no character collision. Useful for scouting terrain or taking screenshots. Press **V** again to return to third-person.

## Display

| Input | Action |
|-------|--------|
| F | Toggle browser fullscreen |

## Debug

| Input | Action |
|-------|--------|
| B | Toggle debug mode |

Debug mode adds `#debug` to the URL and reloads the page. In debug mode you get performance stats and developer overlays via lil-gui.

## Side panel buttons

Located on the right edge of the screen:

1. **Music** — toggle ambient background music on/off
2. **X** — open [@pumpyworld](https://x.com/pumpyworld) on X
3. **Docs** — open the official documentation
4. **Info** — open the in-game about panel

All buttons are the same size (2.65rem) with glassmorphism styling.
