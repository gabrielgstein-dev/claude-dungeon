# Sprite Specification

## Spritesheet format

A single PNG file with frames arranged horizontally, grouped by animation row.

### Minimum (no floating)

```
Row 0 — idle:       [f0][f1][f2][f3]          (4 frames)
Row 1 — walk right: [f0][f1][f2][f3][f4][f5]  (6 frames)
Row 2 — walk left:  [f0][f1][f2][f3][f4][f5]  (6 frames)
```

### Full

```
Row 0 — idle:       4 frames
Row 1 — walk right: 6–8 frames
Row 2 — walk left:  6–8 frames
Row 3 — talk:       2–4 frames (mouth open/closed)
Row 4 — run right:  6–8 frames  (optional)
Row 5 — run left:   6–8 frames  (optional)
```

## Recommended sizes

| Frame size | Scale | Result on screen |
|------------|-------|-----------------|
| 16×16 px   | 3×    | 48×48 px        |
| 32×32 px   | 2×    | 64×64 px        |

16×16 is the recommended base size — matches the default tileset grid.

## Tools

- [Aseprite](https://www.aseprite.org/) — industry standard, paid (~$20)
- [LibreSprite](https://libresprite.github.io/) — free fork of Aseprite
- [Piskel](https://www.piskelapp.com/) — free, browser-based

## Configuration file

Place both files inside your project's `.claude/` folder:

```
your-project/
  .claude/
    pixel-agent.json
    sprite.png
```

### pixel-agent.json

```json
{
  "name": "Your Name",
  "sprite": "./sprite.png",
  "frameWidth": 16,
  "frameHeight": 16,
  "animations": {
    "idle":       { "row": 0, "frames": 4, "fps": 6  },
    "walkRight":  { "row": 1, "frames": 6, "fps": 12 },
    "walkLeft":   { "row": 2, "frames": 6, "fps": 12 },
    "talk":       { "row": 3, "frames": 4, "fps": 8  }
  }
}
```

Fields not provided fall back to the default sprite animation.
