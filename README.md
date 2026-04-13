# claude-dungeon

Visualize your Claude Code agents as heroes exploring a dungeon that **maps your repository structure**.

Each folder becomes a room. Each open GitHub issue becomes a boss. When a PR is merged, the room is cleared.

## Concept

| Dungeon          | Your repository                              |
|------------------|----------------------------------------------|
| Room             | Module / folder in the project               |
| Boss             | Open GitHub issue                            |
| Loot             | Merged PR / closed issue                     |
| Hero exploring   | Claude agent editing files in that module    |
| Cleared room     | Tests passing / issue closed                 |

## Status

> Early development. See [docs/brainstorm.md](docs/brainstorm.md) for the full vision.

## Roadmap

- [ ] **Phase 1 — MVP:** Claude Code hook → hero walks to the room of the file being edited
- [ ] **Phase 2 — GitHub:** Issues as bosses, PRs as quests
- [ ] **Phase 3 — Living dungeon:** Persistent state, history, visual hot spots

## Custom Sprites

Bring your own hero. Add a spritesheet to `.claude/sprite.png` and configure `.claude/pixel-agent.json`:

```json
{
  "name": "Your Name",
  "sprite": "./sprite.png",
  "frameWidth": 16,
  "frameHeight": 16,
  "animations": {
    "idle": [0, 1, 2, 3],
    "walk": [4, 5, 6, 7]
  }
}
```

If no custom sprite is provided, a default hero is used.

## Tech Stack

- **Frontend:** React + TypeScript + Vite
- **Rendering:** Canvas 2D (no game engine — lightweight)
- **Real-time:** WebSocket
- **Backend:** Node.js + Express
- **Hooks:** Claude Code JSONL file watcher
- **GitHub:** Webhooks + Octokit
