# Architecture and decisions

## Principles

1. **The simulation is a pure library.** `src/engine` has no React, no DOM and no randomness it does not own; the UI is a client of it. That is what makes the game deterministic, testable and replayable.
2. **Organise by feature, not file type.** A feature owns its components, hooks, styles and tests. Shared code flows one way: `shared → engine/game → features → app`.
3. **Features do not import each other.** They meet in `app/` (routes, shell). ESLint enforces it (`no-restricted-imports`), and also keeps React out of `engine`.
4. **Accessibility comes from primitives**, not hand-rolled ARIA: tabs, dialogs and tooltips use Base UI.
5. **One source of truth for design.** Colour, space, radius, type and motion are CSS custom properties in `styles/tokens.css`; components never hard-code them.

## Code map

```
src/
  app/        entry, providers, routes (React Router, hash + lazy), shell (top nav), dialogs
  engine/     pure simulation: catalog, arch, engine, net, cost, analysis, telemetry, attack, lint, geo, params
  content/    data: missions, academy lessons, sandbox templates, journey stages, traffic shapes, paths, game days
  game/       replayable session state machine, scoring, progression, zustand store + persistence, hooks
  entities/   domain widgets shared by several features (mission buttons)
  features/   home, missions, incidents, academy, journal, achievements, paths, workspace (the canvas for missions and the Lab)
  shared/     ui (design-system components on Base UI), lib, hooks
  styles/     tokens, base, layout, components, features, responsive: CSS cascade layers
```

- **Persistence by replay.** A session is an ordered log of operations (advance, deploy, action, chaos, revert, game day). Resuming replays it deterministically, so saves stay small and exact.
- **Backend boundary.** The store is the only persistence consumer (`zustand/persist`, debounced); simulation and scoring are pure functions, so a backend could be added behind the store.

## Decisions

- **React 19, Vite 7, `@vitejs/plugin-react` 5.** Vite stays on 7: Vitest 4 with Vite 8 produced peer-resolution failures, and plugin-react 6 drops the `babel` option, which would block the React Compiler. Revisit when Vitest's Vite 8 support settles.
- **React Router 8, library mode, hash router, lazy routes.** The app is a static site with no server rewrites, so hash routing is deliberate. Every page is a lazy route, so the landing page ships without the simulator, canvas or charts. Deep links (`#/play/<id>`, `#/lab?template=`) keep working.
- **Base UI for accessible primitives**, wrapped once in `shared/ui` so swapping it is a one-file change. It has a stable 1.x and is unstyled; React Aria was considered and rejected as too heavy for three primitives.
- **Layered, token-driven plain CSS, no Tailwind.** `@layer reset, tokens, base, layout, components, features, utilities`. The identity is a dense bespoke dashboard, mostly custom CSS in any framework, and migrating about 350 rules to utilities has regression risk and no user benefit. Tokens map one-to-one to Tailwind's `@theme` if that changes. *Embedding caveat:* unlayered CSS beats layered CSS, so a host page that injects `body { background: #fff }` or `:root { color-scheme: light }` would override the theme. The few global rules the theme depends on (colour scheme, `html`/`body` background, text colour, font) therefore live unlayered at the end of `styles/index.css`, and `tokens.test.ts` guards that and the WCAG AA contrast of the text tokens.
- **ESLint 10 flat config + Prettier; React Compiler not enabled.** The hooks rules run in lint, so the code is compiler-ready. The canvas derives memoised data from a deliberately mutable session object and a `tick` counter, so the compiler could memoise stale reads; enabling it needs a store refactor to immutable snapshots. Biome was rejected because it cannot run the hooks rules.
- **zustand for client state** (session, settings, progress). There is no server state, so no TanStack Query. URL state (mission id, lab template) lives in the router.

## Product notes

- **Identity:** dark command-centre look (navy surfaces, cyan primary, violet for async and learning, amber for warning, red for critical). Three-column workspace: mission and events | canvas and telemetry | components and inspector. Nodes are category-coloured icon tiles; edges are coloured by meaning (traffic cyan, async violet, error red); the canvas shows the real fleet behind each node (instances, replicas, queue depth).
- **Visual states:** healthy, degraded, overloaded, unavailable, insecure, misconfigured, idle, each with a stated cause.
- **Progressive disclosure:** the sandbox unlocks component tiers by level (or all, in Settings); missions expose only relevant components.
- **Responsive:** side columns collapse on desktop; tablet and phone get a single pane with bottom navigation.
- **Accessibility:** landmarks, labelled controls, dialogs with focus handling, keyboard-operable controls, `prefers-reduced-motion` respected, status never conveyed by colour alone.
