# Architecture

**Analysis Date:** 2026-04-10

## Pattern Overview

**Overall:** Monorepo with a package-centric library + demo application split.

**Key Characteristics:**
- Workspace orchestration at repository root, with package boundaries in `apps/*` and `components/*` (`package.json`, `pnpm-workspace.yaml`, `lerna.json`).
- Core production logic isolated in a single plugin module at `components/jquery/src/particles.ts`, then compiled and bundled into `components/jquery/dist/*`.
- Demo delivery separated as an Express static server at `apps/jquery/app.js` with browser-side orchestration in `apps/jquery/public/javascripts/demo.js` and template rendering in `apps/jquery/views/index.pug`.

## Layers

**Workspace/Build Orchestration Layer:**
- Purpose: Coordinate multi-package build and CI execution.
- Location: `package.json`, `nx.json`, `lerna.json`, `.github/workflows/nodejs.yml`.
- Contains: Workspace declarations, task aliases, caching defaults, CI job definitions.
- Depends on: `pnpm`, `lerna`, `nx`.
- Used by: Both package pipelines (`components/jquery` and `apps/jquery`).

**Library Source Layer:**
- Purpose: Implement the jQuery plugin API surface for tsParticles.
- Location: `components/jquery/src/particles.ts`.
- Contains: jQuery extension (`$.fn.particles`), type aliases (`ParticlesResult`, `IParticlesProps`), initialization and JSON-loading methods.
- Depends on: `@tsparticles/engine`, global jQuery runtime/types.
- Used by: Library build pipeline and downstream browser consumers (including demo app via bundled output).

**Library Build/Distribution Layer:**
- Purpose: Transform TypeScript source into distributable JS, minified JS, and declarations.
- Location: `components/jquery/package.json`, `components/jquery/tsconfig.json`, `components/jquery/rollup.config.mjs`, `components/jquery/dist/*`.
- Contains: Compile (`tsc`), bundle (`rollup`), minify (`uglifyjs`) sequence.
- Depends on: TypeScript compiler, Rollup/Babel, UglifyJS.
- Used by: npm package consumers and demo static asset mount (`/jquery-particles` in `apps/jquery/app.js`).

**Demo Server Layer:**
- Purpose: Host example UI and expose static assets for runtime experimentation.
- Location: `apps/jquery/app.js`.
- Contains: Express app setup, view engine config, static mounts for local `public` assets and `node_modules` dependencies, root route.
- Depends on: `express`, `stylus`, optional `helmet`/`express-rate-limit` (present but commented).
- Used by: Local developers running `pnpm run start` in `apps/jquery`.

**Demo Frontend Layer:**
- Purpose: Provide interactive preset selection and runtime editing against plugin API.
- Location: `apps/jquery/views/index.pug`, `apps/jquery/public/javascripts/demo.js`, `apps/jquery/public/stylesheets/main.styl`.
- Contains: DOM skeleton and script wiring, JSON editor integration, stats panel updates, particle refresh interactions.
- Depends on: jQuery, tsParticles bundle/configs, lodash, JSONEditor, stats.ts, plugin bundle.
- Used by: Browser clients of the demo server.

## Data Flow

**Plugin Runtime Initialization Flow:**

1. Browser loads scripts declared in `apps/jquery/views/index.pug`, including jQuery, `tsparticles.bundle.min.js`, configs, and `/jquery-particles/jquery.particles.js`.
2. `apps/jquery/public/javascripts/demo.js` calls `$('#tsparticles').particles().init(...)`.
3. `components/jquery/src/particles.ts` extension executes `tsParticles.load({ id, options })` (or URL mode with `ajax`) and resolves container callback.
4. Demo layer updates JSON editor and stats panels based on `tsParticles.domItem(0)` state in `apps/jquery/public/javascripts/demo.js`.

**Build-to-Distribution Flow:**

1. Source `components/jquery/src/particles.ts` compiles to `components/jquery/dist/particles.js` + `components/jquery/dist/particles.d.ts` via `tsc` (`components/jquery/package.json`, `components/jquery/tsconfig.json`).
2. Rollup uses `components/jquery/rollup.config.mjs` to emit IIFE `components/jquery/dist/jquery.particles.js`.
3. UglifyJS emits `components/jquery/dist/jquery.particles.min.js` and sourcemap.
4. Demo server maps package dist folder through `app.use('/jquery-particles', express.static('./node_modules/@tsparticles/jquery/dist'))` in `apps/jquery/app.js`.

**State Management:**
- Client state is browser-local and imperative: selected preset persisted in `localStorage` and active particle container accessed via `tsParticles.domItem(0)` (`apps/jquery/public/javascripts/demo.js`).
- No backend persistence layer is present; server is stateless request/asset serving in `apps/jquery/app.js`.

## Key Abstractions

**jQuery Plugin Facade (`particles()`):**
- Purpose: Wrap tsParticles engine loading into idiomatic jQuery chainable usage.
- Examples: `components/jquery/src/particles.ts`.
- Pattern: Extend global `JQuery` interface and attach behavior through `$.fn` returning method object (`init`, `ajax`).

**Container Callback Contract:**
- Purpose: Provide post-load hook with container handle.
- Examples: callback signatures in `components/jquery/src/particles.ts`; consumer callbacks in `README.md` and `apps/jquery/public/javascripts/demo.js`.
- Pattern: Promise-based `tsParticles.load(...).then(callback)` delegation.

**Demo Asset Gateway:**
- Purpose: Normalize browser access to dependency assets under stable URL prefixes.
- Examples: static mappings in `apps/jquery/app.js` (`/tsparticles`, `/demo-configs`, `/jquery-particles`, `/jquery`, etc.).
- Pattern: Express static proxying from `node_modules` directories.

## Entry Points

**Workspace Build Entry Point:**
- Location: root `package.json` scripts (`build`, `build:ci`, `build:lerna`, `build:nx`).
- Triggers: Local build commands and CI pipeline steps in `.github/workflows/nodejs.yml`.
- Responsibilities: Fan out package builds across monorepo.

**Library Source Entry Point:**
- Location: `components/jquery/src/particles.ts`.
- Triggers: TypeScript compile process and runtime import of built bundle.
- Responsibilities: Define and export plugin behavior.

**Demo Server Entry Point:**
- Location: `apps/jquery/app.js`.
- Triggers: `pnpm run start` in `apps/jquery/package.json`.
- Responsibilities: Start HTTP server, render root page, serve styles/scripts/assets.

**Demo Client Entry Point:**
- Location: `apps/jquery/public/javascripts/demo.js`.
- Triggers: Included as terminal script in `apps/jquery/views/index.pug`.
- Responsibilities: Initialize presets/editor/stats and bind UI events.

## Error Handling

**Strategy:** Minimal inline handling, delegated to runtime libraries and browser behavior.

**Patterns:**
- Async chaining through promises without centralized catch blocks in plugin methods (`components/jquery/src/particles.ts`).
- UI-level alert for editor parsing/runtime errors via `onError` callback (`apps/jquery/public/javascripts/demo.js`).

## Cross-Cutting Concerns

**Logging:** Console logging used only for server startup status in `apps/jquery/app.js`.
**Validation:** Input/options validation is delegated to tsParticles engine and JSONEditor UI constraints (`components/jquery/src/particles.ts`, `apps/jquery/public/javascripts/demo.js`).
**Authentication:** Not applicable in current architecture; demo server exposes public static/demo routes only (`apps/jquery/app.js`).

---

*Architecture analysis: 2026-04-10*
