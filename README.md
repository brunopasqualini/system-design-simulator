# Engineering Ops: a software architecture and system design simulator

_Build. Break. Learn._ **Live app: <https://brunopasqualini.github.io/engineering-ops/>** (free, in your browser, no signup).

Design AWS-style cloud architectures, deploy them, watch how they behave under load and failure, investigate incidents from logs, metrics, traces and change history, and learn from the consequences rather than from memorising service names. Fourteen missions, a sandbox Lab with chaos drills, traffic shapes, game days and attack simulations, and scoring across ten dimensions.

For aspiring engineers, working software, cloud, DevOps and platform engineers who want practice beyond tutorials and certification questions, and developers growing into system design.

- React + TypeScript + Vite. No backend, no account, no cloud calls.
- A deterministic, rule-based simulation engine (pure TypeScript, unit-tested) with explainable causal effects.
- Everything is **simulated**: costs are estimates, not AWS prices, and nothing touches a real cloud account.

Topics: system-design, software-architecture, cloud-architecture, aws, sre, devops, chaos-engineering, incident-response, simulator, learning, react, typescript, vite.

## Run it

```bash
npm install
npm run dev        # http://localhost:5173
npm run check      # typecheck + lint + tests
npm run build      # static site in dist/ (relative base, host it anywhere)
```

Also: `npm test`, `npm run typecheck`, `npm run lint`, `npm run format`, `npm run preview`. Needs Node 20+. Progress is saved in the browser (`localStorage`); Settings → Export/Import moves it between browsers.

## How to play

1. Pick a mission and read the briefing, constraints and success criteria.
2. **Investigate** with the dock panels under the canvas: Metrics, Logs, Traces, Dependencies, Changes, Database, Queues, Network, Cost, Security, Alerts. In incidents, outside beginner mode, opening a panel costs a simulated minute.
3. **Hypothesise** the root cause in the mission panel. Diagnosis quality is scored.
4. **Design:** drag components from the library, connect them, configure them in the inspector. Edits are a draft.
5. **Deploy** from Plan & deploy: review the diff, validation and cost change, then deploy. The simulator explains what changed and why (for example "replicas 4→8 raised DB connections 80→160 of 100; 37% of new connections are refused").
6. Fix side effects, revert, or retry; then **verify** with a load test, which scores ten dimensions and shows a debrief.

Keys: `Space` run/pause, `.` step one minute, `Delete` remove the selection.

## Features

| Feature                     | Where                               | What it does                                                                                                                                           | More                                        |
| --------------------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------- |
| Learning paths              | `/paths`                            | Role-based routes (backend, cloud architect, SRE, FinOps) over the missions, with a next-step view.                                                    | [LEARNING_PATHS.md](docs/LEARNING_PATHS.md) |
| Timeline scrubber           | under the health strip; `[` and `]` | Look back at the canvas and health numbers at an earlier minute. Read-only: running, stepping or deploying returns to live.                            |                                             |
| Undo, redo and multi-select | canvas                              | `Ctrl/⌘+Z` and `Ctrl/⌘+Shift+Z` for the draft; Shift/Ctrl/⌘-click or Shift-drag to select several; `Ctrl/⌘+C`, `V`, `D` copy, paste and duplicate.     |                                             |
| Design review               | Plan & deploy                       | Flags anti-patterns in your draft as you build, each with the reason, the fix and a lesson link. Advice, never a deploy gate.                          | [DESIGN_REVIEW.md](docs/DESIGN_REVIEW.md)   |
| Attack simulation           | Lab                                 | Runs a leaked credential, an open data store or an HTTP flood against the deployed design and marks the blast radius on the canvas.                    | [ATTACKS.md](docs/ATTACKS.md)               |
| Users and Regions           | Lab                                 | Latency by user geography, how DNS routes, what losing the primary Region costs, replication lag and cost.                                             | [MULTI_REGION.md](docs/MULTI_REGION.md)     |
| Game days                   | Lab                                 | Scripted multi-fault exercises graded on impact, recovery and detection.                                                                               | [GAME_DAYS.md](docs/GAME_DAYS.md)           |
| Traffic shapes              | Lab                                 | Daily cycle, flash sale, thundering herd, batch sawtooth and going viral.                                                                              | [SIMULATION.md](docs/SIMULATION.md)         |
| Canvas effects              | canvas toolbar                      | Animated traffic and component effects while the simulation runs (Subtle, Full, Off).                                                                  | [CANVAS_EFFECTS.md](docs/CANVAS_EFFECTS.md) |
| Feedback                    | mission debrief, Settings           | Rate a mission and add a note. Stored in this browser only, separate from the game save; you can copy it, export it or open a pre-filled GitHub issue. |                                             |

A new feature adds a row here and a file under `docs/`.

## Missions

| #   | Mission                     | Kind     | Teaches                                                                                                                                  |
| --- | --------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | The Growing Monolith        | project  | cohesion/coupling, modular monolith vs microservices, shared-database coupling, ADRs                                                     |
| 2   | The Database Meltdown       | incident | bottleneck analysis, indexes, connection pooling, caching trade-offs, why scaling the app tier backfires                                 |
| 3   | The Unreliable Deployment   | incident | CI/CD, integration tests, canary/blue-green, auto-rollback, feature flags, blast radius                                                  |
| 4   | The AWS Networking Puzzle   | project  | VPC, subnets, routes, IGW/NAT, security groups vs NACLs, IAM least privilege — with a layer-by-layer network inspector                   |
| 5   | The Queue That Never Drains | incident | SQS semantics, visibility timeout, DLQs, idempotency, backpressure, eventual consistency                                                 |
| 6   | The Cloud Bill Shock        | project  | right-sizing, lifecycle, NAT/data-transfer, log costs, tags, cost vs reliability                                                         |
| 7   | The N+1 Endpoint            | project  | N+1 queries, fixing the cause vs a bigger database or a cache, queries per request as a budget                                           |
| 8   | The Impatient Client        | project  | timeouts as contracts, retry budgets, circuit breakers, why replicas do not fix a slow dependency                                        |
| 9   | The Merger (capstone)       | project  | competing requirements, what to converge, data ownership, audit and peak readiness, ADRs; **inherits debts from missions 1, 2, 7 and 8** |
| 10  | The Stale Receipt           | incident | asynchronous replication, replica lag, read-your-writes, why capacity lowers lag but never removes it                                    |
| 11  | The Hot Key                 | incident | DynamoDB per-item limits, throttling with spare capacity, write sharding and what it costs                                               |
| 12  | The Noisy Neighbour         | incident | per-client rate limiting, why a global limit and more capacity fail, backpressure with 429s                                              |
| 13  | The Regional Outage         | project  | Region versus zone resilience, RTO and RPO, backup and restore, pilot light, warm standby, active/active                                 |
| 14  | The Blank Page              | project  | designing from requirements: contended inventory, short-TTL caching, zone survival, complexity as a cost                                 |

Journey order: level 1 = missions 2, 7, 8; level 2 = 1, 5, 10; level 3 = 4, 6, 11; level 4 = 3, 12, 13; level 5 = 14, 9. New players start with mission 2: something visibly fails, which teaches the investigate-and-fix loop faster than an open brief. Every mission has several valid solutions and tempting wrong ones; `src/game/missions.test.ts` encodes both.

## Docs

[Architecture and decisions](docs/ARCHITECTURE.md) · [Simulation model](docs/SIMULATION.md) · [Authoring missions](docs/MISSIONS.md) · [Learning paths](docs/LEARNING_PATHS.md) · [Design review](docs/DESIGN_REVIEW.md) · [Attacks](docs/ATTACKS.md) · [Multi-Region](docs/MULTI_REGION.md) · [Game days](docs/GAME_DAYS.md) · [Canvas effects](docs/CANVAS_EFFECTS.md)

## Deployment

This repo does not commit its build. A push to `main` runs `.github/workflows/ci.yml` (typecheck, lint, tests, build), then sends a `repository_dispatch` event (`project-updated`) to [brunopasqualini.github.io](https://github.com/brunopasqualini/brunopasqualini.github.io), which builds the app (`npm ci && npm run build`), copies `dist/` to `/engineering-ops/` and deploys the site. `/engineering-ops/dist/` redirects to `/engineering-ops/` and keeps the `#/…` route.

Needs the secret `SITE_DISPATCH_TOKEN` in this repo: a fine-grained token on `brunopasqualini/brunopasqualini.github.io` with **Contents: Read and write** (required to send `repository_dispatch`).

## Limits

A teaching model: small, internally consistent and defensible, not a faithful cloud simulator. Terraform, IAM and pipeline artifacts are educational and non-executable. Deployment strategies and multi-Region behaviour are deliberately coarse.
