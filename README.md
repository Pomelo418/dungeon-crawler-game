# Dungeon Crawler

A turn-based rogue-like dungeon crawler built with vanilla HTML, CSS, and Canvas. No dependencies, no build step — open the file and play.

```
  ██████╗ ██╗   ██╗███╗   ██╗ ██████╗ ███████╗ ██████╗ ███╗   ██╗
  ██╔══██╗██║   ██║████╗  ██║██╔════╝ ██╔════╝██╔═══██╗████╗  ██║
  ██║  ██║██║   ██║██╔██╗ ██║██║  ███╗█████╗  ██║   ██║██╔██╗ ██║
  ██║  ██║██║   ██║██║╚██╗██║██║   ██║██╔══╝  ██║   ██║██║╚██╗██║
  ██████╔╝╚██████╔╝██║ ╚████║╚██████╔╝███████╗╚██████╔╝██║ ╚████║
  ╚═════╝  ╚═════╝ ╚═╝  ╚═══╝ ╚═════╝ ╚══════╝ ╚═════╝ ╚═╝  ╚═══╝
```

## Quick Start

Download `dungeon.html` and open it in any modern browser. That's it.

## How to Play

| Key | Action |
|-----|--------|
| `WASD` or `↑↓←→` | Move |
| Walk into an enemy | Attack |
| Walk over `★` | Collect key |
| Reach `▶` with all keys | Exit the level |
| `Space` or `.` | Wait a turn |
| `N` | New game |

**Goal:** collect all 3 keys, then reach the exit `▶`. Each cleared level sends you deeper — enemies grow stronger, but your character levels up too.

## Enemies

| Glyph | Name | Notes |
|-------|------|-------|
| `g` | Goblin | Fast, fragile |
| `s` | Skeleton | Balanced |
| `z` | Zombie | Slow (every other turn), tanky |
| `O` | Orc | Slow, hits hard |

## Technical Implementation

The interesting parts are three classical algorithms wired together:

### Maze Generation — Recursive Backtracker
The dungeon is a grid initialized to all-walls. A depth-first search starts at `(1,1)`, randomly shuffles its four cardinal directions, and carves a passage to each unvisited neighbour (stepping 2 tiles at a time, removing the wall in between). After the DFS completes, extra wall cells are punched through to create loops and shortcuts, preventing the "one correct path" feel of a perfect maze.

### Field of View — Bresenham Line-of-Sight
Each turn, the game casts a ray from the player to every tile within radius 7 using Bresenham's line algorithm. A tile is **visible** if the ray reaches it without hitting a wall first. Two separate grids track state: `vis` (currently lit this turn) and `explored` (ever seen), so the map dims rather than disappears when you leave an area.

### Enemy Pathfinding — Breadth-First Search
When an enemy is within chase range, it runs a BFS from its position toward the player, capped at 12 tiles. The path is reconstructed by walking back through a `parent` map, and the enemy moves one step along it. Enemies outside range wander with 35% probability per turn. Orcs and zombies skip every other turn to simulate slower movement.

### State Model
All game state lives in a single `G` object (map, entity lists, visibility grids, player stats, turn counter). Mutations flow one way: input handlers mutate `G`, then `render()` reads `G` to redraw. No framework — it's a hand-rolled unidirectional data flow.

### Performance Notes
- Map and visibility grids use `Uint8Array` instead of plain JS arrays — typed, cache-friendly, minimal overhead for tight inner loops.
- BFS is depth-capped, keeping each enemy's pathfinding O(constant) regardless of map size.
- Canvas redraws the full map each turn (straightforward for a 41×27 grid at 18px tiles).

## Stack

- **Rendering:** HTML5 Canvas API (`CanvasRenderingContext2D`)
- **Language:** Vanilla JavaScript (ES2020)
- **Styling:** Plain CSS
- **Dependencies:** None
- **Build tools:** None

## Project Structure

```
dungeon.html   ← the entire game: markup, styles, and ~850 lines of JS
```
