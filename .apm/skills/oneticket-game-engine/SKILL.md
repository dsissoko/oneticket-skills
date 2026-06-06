---
name: oneticket-game-engine
description: Build 2D browser games using HTML5 Canvas API and React. Use when implementing game loops, collision detection, player controls, rendering, or game state management in a React+Vite+TypeScript stack.
version: 1.0.0
---

# game-engine

Build 2D browser games with Canvas API and React. No external game framework — pure Canvas + requestAnimationFrame + TypeScript.

---

## Core Architecture

### Canvas vs React — strict separation

- **Canvas** — game rendering surface (game objects, sprites, animations, particles)
- **React state** — UI only (score, lives, menu, game-over screen, HUD overlays)
- **Refs** — fast access to game state inside animation loops (never React state for game loop)

Do not run the game through DOM layout. Do not use React components as game objects.

### Dual State Management

```ts
// React state — triggers re-renders for UI
const [score, setScore] = useState(0);
const [gameState, setGameState] = useState<'menu' | 'playing' | 'gameover'>('menu');

// Refs — game loop access, no re-render cost
const gameRef = useRef<GameState>({ player, enemies, bullets, ... });
const animFrameRef = useRef<number>(0);
```

---

## Game Loop

```ts
const gameLoop = useCallback((timestamp: number) => {
  const deltaTime = (timestamp - lastTimeRef.current) / 1000; // seconds
  lastTimeRef.current = timestamp;

  update(deltaTime);   // physics, logic, collisions
  render();            // clear + draw
  
  animFrameRef.current = requestAnimationFrame(gameLoop);
}, []);

// Start
useEffect(() => {
  animFrameRef.current = requestAnimationFrame(gameLoop);
  return () => cancelAnimationFrame(animFrameRef.current);
}, [gameLoop]);
```

**Always use delta time** — ensures consistent speed regardless of frame rate.

---

## Canvas Setup

```ts
const canvasRef = useRef<HTMLCanvasElement>(null);

useEffect(() => {
  const canvas = canvasRef.current!;
  const ctx = canvas.getContext('2d')!;
  // Set logical dimensions — not CSS size
  canvas.width = GAME_WIDTH;
  canvas.height = GAME_HEIGHT;
}, []);
```

Clear each frame before drawing:
```ts
ctx.clearRect(0, 0, canvas.width, canvas.height);
```

---

## Collision Detection (AABB)

```ts
interface Rect { x: number; y: number; width: number; height: number; }

function collides(a: Rect, b: Rect): boolean {
  return (
    a.x < b.x + b.width &&
    a.x + a.width > b.x &&
    a.y < b.y + b.height &&
    a.y + a.height > b.y
  );
}
```

Use AABB for most 2D arcade games. Add circle collision only when shape truly requires it.

---

## Input Handling

```ts
const keysRef = useRef<Set<string>>(new Set());

useEffect(() => {
  const onKeyDown = (e: KeyboardEvent) => {
    e.preventDefault();
    keysRef.current.add(e.key);
  };
  const onKeyUp = (e: KeyboardEvent) => keysRef.current.delete(e.key);
  
  window.addEventListener('keydown', onKeyDown);
  window.addEventListener('keyup', onKeyUp);
  return () => {
    window.removeEventListener('keydown', onKeyDown);
    window.removeEventListener('keyup', onKeyUp);
  };
}, []);

// In update():
if (keysRef.current.has('ArrowLeft')) player.x -= SPEED * dt;
```

---

## Rendering Patterns

```ts
// Filled rectangle
ctx.fillStyle = '#00ff00';
ctx.fillRect(entity.x, entity.y, entity.width, entity.height);

// Text
ctx.fillStyle = '#ffffff';
ctx.font = '24px monospace';
ctx.fillText(`Score: ${score}`, 10, 30);

// Image / Sprite
ctx.drawImage(sprite, entity.x, entity.y, entity.width, entity.height);
```

---

## Rules

- Separate simulation from rendering — never put game logic inside draw calls
- Use delta time in all movement calculations — never fixed pixel values per frame
- Clean up requestAnimationFrame and event listeners in useEffect return
- Prefer custom simple collision over a physics engine for arcade games
- Keep world coordinates separate from CSS/screen coordinates
- Use object pooling for frequently created/destroyed objects (bullets, particles)
