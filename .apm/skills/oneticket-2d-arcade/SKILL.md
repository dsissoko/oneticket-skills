---
name: oneticket-2d-arcade
description: "Patterns for 2D arcade games (Space Invaders, Breakout, Pac-Man style). Use when implementing enemy formations, projectiles, wave progression, lives, scoring, or classic arcade game states."
version: 1.0.0
---

# oneticket-2d-arcade

Reusable patterns for classic 2D arcade games. Covers the mechanics common to Space Invaders, Breakout, Galaga, and similar fixed-screen arcade games.

Use alongside `game-engine` for the Canvas/React foundation.

---

## Game States

```ts
type GameState = 'menu' | 'playing' | 'paused' | 'gameover' | 'victory';
```

State transitions:
- `menu` → `playing` on start action
- `playing` → `paused` on Escape
- `playing` → `gameover` on lives = 0
- `playing` → `victory` on all enemies/targets cleared
- `gameover` | `victory` → `menu` on restart

---

## Player

```ts
interface Player {
  x: number; y: number;
  width: number; height: number;
  speed: number;          // px/s
  lives: number;
  invincible: boolean;    // brief invincibility after hit
  invincibleTimer: number;
}
```

- Constrain movement to canvas bounds
- Flash sprite during invincibility frames
- Respawn at center bottom after losing a life

---

## Enemy Formation (Space Invaders pattern)

```ts
interface Enemy {
  x: number; y: number;
  width: number; height: number;
  alive: boolean;
  row: number; col: number;
  points: number;         // higher rows worth more
}

interface Formation {
  enemies: Enemy[];
  dx: number;             // current horizontal direction (+1 / -1)
  speed: number;          // px/s — increases as enemies die
  dropDistance: number;   // px to drop on direction change
  shootInterval: number;  // ms between enemy shots
}
```

Formation movement:
1. Move all alive enemies horizontally by `dx * speed * dt`
2. If any enemy reaches a canvas edge → flip `dx`, drop formation by `dropDistance`
3. Increase speed as fewer enemies remain: `speed = baseSpeed * (1 + (total - alive) / total)`

---

## Projectiles

```ts
interface Projectile {
  x: number; y: number;
  width: number; height: number;
  dy: number;    // negative = upward (player), positive = downward (enemy)
  active: boolean;
}
```

Object pool pattern — pre-allocate, reuse:
```ts
const POOL_SIZE = 20;
const pool: Projectile[] = Array.from({ length: POOL_SIZE }, () => ({ active: false, ... }));

function spawn(x: number, y: number, dy: number) {
  const p = pool.find(p => !p.active);
  if (p) { p.x = x; p.y = y; p.dy = dy; p.active = true; }
}
```

Deactivate when off-screen:
```ts
if (p.y < 0 || p.y > canvas.height) p.active = false;
```

---

## Collision Resolution

Process order each frame:
1. Player bullets vs enemies → deactivate bullet + enemy, add score
2. Enemy bullets vs player → deactivate bullet, reduce lives, trigger invincibility
3. Enemy bullets vs shields → degrade shield segment
4. Enemies reaching bottom → game over

---

## Shields / Destructible Terrain

```ts
interface ShieldSegment {
  x: number; y: number; width: number; height: number;
  health: number;   // 3 = intact, 2 = damaged, 1 = critical, 0 = destroyed
}
```

Draw shield with opacity based on health:
```ts
ctx.globalAlpha = segment.health / 3;
ctx.fillRect(segment.x, segment.y, segment.width, segment.height);
ctx.globalAlpha = 1;
```

---

## Scoring

```ts
const POINTS = {
  row0: 30,  // top row — highest value
  row1: 20,
  row2: 10,
  bonus: 100, // mystery ship / special target
};

// Combo multiplier (optional)
let combo = 0;
// On kill: combo++; score += points * (1 + combo * 0.1);
// On player hit: combo = 0;
```

---

## Wave Progression

```ts
let wave = 1;

function startNextWave() {
  wave++;
  formation.speed *= 1.2;          // faster each wave
  formation.shootInterval *= 0.85; // more frequent shooting
  resetFormation();
}
```

Trigger when `formation.enemies.every(e => !e.alive)`.

---

## HUD (React overlay)

Render score, lives, wave as React components above the canvas — not on canvas.

```tsx
<div className="hud">
  <span>Score: {score}</span>
  <span>Lives: {'♥'.repeat(lives)}</span>
  <span>Wave: {wave}</span>
</div>
```

---

## Rules

- Enemy formation speed must increase as enemies die — static speed breaks the arcade feel
- Player shooting: max 1-2 active bullets at a time (classic constraint)
- Invincibility frames after player hit: ~2 seconds, visible flashing
- Game over triggers when: lives = 0 OR enemies reach bottom boundary
- Victory triggers when: all enemies destroyed
- Always reset combo on player hit
