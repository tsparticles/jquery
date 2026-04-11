# Architecture Research

**Domain:** Monorepo modernization for `@tsparticles/jquery` plugin + demo app
**Researched:** 2026-04-10
**Confidence:** HIGH

## Standard Architecture

### System Overview

```text
+-------------------------------------------------------------------+
|                    Workspace Orchestration Layer                  |
|        (pnpm workspaces + Lerna/Nx + CI workflow scripts)        |
+-------------------------------+-----------------------------------+
                                |
                                v
+-------------------------------+-----------------------------------+
|                     Plugin Package Layer                          |
|  components/jquery/src (API facade)                              |
|  components/jquery build pipeline (tsc -> rollup -> minify)      |
|  components/jquery/dist (published artifacts)                     |
+-------------------------------+-----------------------------------+
                                |
                                v
+-------------------------------+-----------------------------------+
|                       Demo Application Layer                      |
| apps/jquery/app.js (Express static gateway)                       |
| apps/jquery/views + public/javascripts (browser behavior)         |
+-------------------------------+-----------------------------------+
                                |
                                v
+-------------------------------+-----------------------------------+
|                   Browser Runtime Dependencies                    |
| jQuery global + tsParticles bundles + demo configs                |
+-------------------------------------------------------------------+
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Workspace orchestration | Defines package graph, task order, and CI execution entrypoints | Root `package.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`, GitHub workflow |
| Plugin source boundary | Owns public jQuery plugin contract and tsParticles adaptation logic | `components/jquery/src/particles.ts` |
| Plugin build boundary | Converts source into distributable JS/types with stable filenames | `tsc`, `rollup`, minify scripts in `components/jquery/package.json` |
| Demo server boundary | Serves page shell and maps package artifacts from `node_modules` into browser paths | `apps/jquery/app.js` static mounts |
| Demo frontend boundary | Exercises plugin API and exposes manual verification surface | `apps/jquery/public/javascripts/demo.js`, `apps/jquery/views/index.pug` |

## Recommended Project Structure

```text
apps/
  jquery/
    app.js                   # Demo server and static path gateway
    views/                   # HTML shell for runtime script ordering
    public/                  # Demo-only browser code and styling

components/
  jquery/
    src/                     # Plugin API source and type surface
    dist/                    # Build outputs consumed by users and demo
    tsconfig.json            # TypeScript compile configuration
    rollup.config.mjs        # Bundle compatibility output config

root
  package.json              # Workspace task entrypoints
  pnpm-workspace.yaml       # Package graph
  lerna.json / nx.json      # Orchestration compatibility and caching
  .github/workflows/        # CI execution and version matrix
```

### Structure Rationale

- **Boundary-first modernization:** Keep plugin runtime logic in `components/jquery/src` and demo logic in `apps/jquery/public` so dependency and syntax updates do not blur ownership.
- **Artifact contract stability:** Keep `components/jquery/dist` filenames and demo mount path behavior stable during migration so consumers are unaffected while internals change.
- **Monorepo as integration harness:** Use workspace orchestration to validate plugin and demo together after each migration slice.

## Architectural Patterns

### Pattern 1: Contract-Preserving Adapter Core

**What:** Modernize internals while preserving the existing `$.fn.particles().init(...)` and `.ajax(...)` contract.
**When to use:** Any migration that touches plugin internals, typing, or engine integration.
**Trade-offs:** Lowest consumer risk, but may temporarily keep some legacy wrapper shape.

**Example:**
```typescript
// Keep public call shape stable, migrate internals behind it.
$.fn.particles = function () {
  return {
    init: (options, cb) => loadWithModernEngine(options, cb),
    ajax: (url, cb) => loadFromUrlWithModernEngine(url, cb),
  };
};
```

### Pattern 2: Build Graph Gating

**What:** Treat plugin build output as an explicit dependency for demo runtime verification.
**When to use:** Dependency upgrades, bundler changes, syntax target changes.
**Trade-offs:** Slightly slower iteration, significantly lower integration regression risk.

**Example:**
```typescript
// Pseudocode task chain
build:plugin -> verify:dist-files -> start:demo -> smoke:runtime
```

### Pattern 3: Incremental Syntax Migration by Boundary

**What:** Migrate syntax/config one boundary at a time (plugin source, plugin build config, demo scripts, root tooling).
**When to use:** Brownfield modernization where behavior parity is required.
**Trade-offs:** More phases, but easier rollback and root-cause isolation.

## Data Flow

### Runtime Flow

```text
Browser page load
  -> app serves script tags and static assets
  -> demo.js invokes $('#tsparticles').particles().init(...)
  -> plugin facade calls tsParticles engine load
  -> engine returns container handle
  -> demo UI reads container state (stats/editor updates)
```

### Build/Release Flow

```text
Root workspace command
  -> plugin TypeScript compile
  -> plugin bundle/minify output in dist/
  -> demo starts and serves plugin dist from node_modules path
  -> manual/CI smoke verifies browser behavior parity
```

### Key Data Flows

1. **API invocation flow (demo -> plugin -> engine):** UI event data flows from demo controls to plugin facade methods, then to tsParticles options loading.
2. **Artifact consumption flow (plugin dist -> demo server -> browser):** Build artifacts flow from `components/jquery/dist` into installed package paths, then through Express static mounts to browser runtime.
3. **Configuration flow (tooling -> package builds):** Root orchestration config controls task ordering and version/runtime compatibility for both app and package.

## Build Order (Risk-Minimizing Sequence)

1. **Stabilize execution baseline first:** Align Node/pnpm/CI/runtime matrix with repository pins before changing code, to avoid false negatives from tool drift.
2. **Modernize plugin dependencies before syntax:** Update tsParticles/jQuery-adjacent deps in `components/jquery` while keeping API contract unchanged; verify dist output names and plugin entry behavior.
3. **Modernize plugin build tooling next:** Migrate TypeScript/rollup/babel/minify settings needed for v4 beta compatibility, then re-verify generated artifact contract.
4. **Integrate demo against new plugin artifacts:** Update demo dependency/tooling only after plugin build is stable; validate static mount paths and script order remain valid.
5. **Apply syntax migration in thin slices:** Refactor source syntax boundary-by-boundary (plugin first, demo second, root scripts last) with behavior checks after each slice.
6. **Consolidate orchestration last:** Reduce overlap between Nx/Lerna script paths only after package and demo are passing, so task-runner migration does not mask runtime regressions.

## Build Order Implications

- **Dependency direction:** Demo depends on plugin dist outputs; plugin pipeline must remain green before demo modernization can be trusted.
- **Contract checkpoints:** Preserve public plugin API and dist artifact names as hard gates between phases.
- **Rollback safety:** Boundary-scoped phases allow reverting one layer (tooling, plugin internals, demo wiring) without undoing full modernization.

## Anti-Patterns

### Anti-Pattern 1: Big-Bang Migration Across Plugin and Demo

**What people do:** Upgrade dependencies, rewrite syntax, and change tooling in one pass across both workspaces.
**Why it's wrong:** Regression source becomes untraceable; API, bundling, and runtime failures overlap.
**Do this instead:** Sequence changes by dependency direction (plugin -> dist -> demo -> root orchestration).

### Anti-Pattern 2: Tooling-First Refactor Without Runtime Contract Gates

**What people do:** Replace build stack and task orchestration before preserving plugin artifact/API contracts.
**Why it's wrong:** Consumer-facing breakage can ship even when builds are green.
**Do this instead:** Define and verify API/dist compatibility checkpoints before and after each tooling change.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| npm registry | Package publish target for plugin artifacts | Keep `dist` contract stable during modernization |
| jsDelivr/CDN docs usage | Browser script delivery pattern for consumers | Ensure output filenames remain backward-compatible |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| `components/jquery` -> `apps/jquery` | Built package artifacts via `node_modules` static mount | Demo is downstream verifier of plugin compatibility |
| Root orchestration -> package/app builds | Scripted task execution and CI pipeline | Use explicit phase gates to isolate failures |

## Sources

- `.planning/PROJECT.md`
- `.planning/codebase/ARCHITECTURE.md`
- `.planning/codebase/STRUCTURE.md`
- `.planning/codebase/STACK.md`
- `.planning/codebase/CONCERNS.md`

---
*Architecture research for: tsParticles jQuery modernization*
*Researched: 2026-04-10*
