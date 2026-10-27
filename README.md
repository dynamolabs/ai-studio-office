# AI Studio Office

A tiny 3D office where a team of AI agents shows up as little characters. Each agent's status decides where it is and what it does:

| Status    | Where the character goes                         |
|-----------|--------------------------------------------------|
| `working` | Sits at a desk and types (screen glows green)    |
| `review`  | Sits at a desk, result ready (screen glows blue) |
| `blocked` | Waits in the glass meeting room, waving          |
| `done`    | Relaxes: sofa, ping-pong, arcade or a short walk |
| `failed`  | Takes a break in the pantry                      |

The coordinator (`role: "coord"`) gets the boss desk and a little crown. Click any character for details; the side panel has a team list and stats (models in use, context used per agent). The scene follows your system light/dark theme for day and night.

It is a single `index.html` with no build step. The only dependency is [three.js r128](https://threejs.org/) from cdnjs.

## Run it

Serve the folder with any static server and open it in a browser:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening `index.html` straight from disk (`file://`) works too, but browsers block `fetch` there, so you will only see the demo team.

It also works on GitHub Pages: enable Pages for the repository (branch `main`, root folder) and the office is live at `https://<user>.github.io/<repo>/`.

## The status feed

Every 30 seconds the page reads `status.json` next to `index.html`. To read a file somewhere else, pass its URL: `index.html?status=https://example.com/my-team.json` (the other server must allow CORS).

If the file cannot be read, the page shows a built-in demo team and a note saying why. When the feed is live, the **Demo team** button toggles between your team and the demo.

### Format

```json
{
  "updatedAt": "2026-10-27T10:00:00Z",
  "agents": [
    {
      "id": "code",
      "role": "thread",
      "title": "Programming: enemy AI",
      "status": "working",
      "note": "Writing the pathfinding code",
      "updated": "2026-10-27T09:59:00Z",
      "created": "2026-10-27T08:06:00Z",
      "model": "claude-sonnet-5-5",
      "ctxUsed": 300000,
      "ctxMax": 1000000
    }
  ]
}
```

| Field       | Required | Meaning                                                                 |
|-------------|----------|-------------------------------------------------------------------------|
| `id`        | yes      | Stable, unique id. A new id walks in through the front door.            |
| `role`      | no       | `"coord"` for the coordinator (boss desk), anything else is a worker.   |
| `title`     | no       | Name shown on the label.                                                |
| `status`    | no       | `working`, `review`, `blocked`, `done` or `failed`. Anything else is idle. |
| `note`      | no       | Short line shown above the character ("what I'm doing").                |
| `updated`, `created` | no | ISO 8601 times, shown in the detail panel.                      |
| `model`     | no       | Model name, used for the "models in use" stats.                         |
| `ctxUsed`, `ctxMax` | no | Tokens used and available, shown as a context meter.             |

A bare array of agents (without the `updatedAt` wrapper) is accepted too.

Anything can write the file: a cron job, a CI step, or the agents themselves ("update your entry in status.json when your status changes"). The page only reads it.

## Credits

The idea of showing agents as people in a cozy Indonesian-style office (rooms for each state, a game room, a street food cart outside) was inspired by [Ruang](https://github.com/yugienugraha/ruang) by yugienugraha (MIT). This project is a separate implementation written from scratch in plain three.js; it shares no code or assets with Ruang.

## License

[MIT](LICENSE) © 2026 dinamo (dynamolabs)
