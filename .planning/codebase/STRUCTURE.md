# Codebase Structure

**Analysis Date:** 2026-04-10

## Directory Layout

```text
jquery/
├── apps/                      # Runnable applications (demo site)
│   └── jquery/                # Express-based jQuery demo app
├── components/                # Publishable packages
│   └── jquery/                # @tsparticles/jquery plugin source and dist
├── .github/workflows/         # CI workflows
├── .planning/codebase/        # Generated architecture/quality/stack mapping docs
├── nx.json                    # Nx task defaults/cache configuration
├── lerna.json                 # Lerna package orchestration/versioning settings
├── pnpm-workspace.yaml        # Workspace package globs
└── package.json               # Root scripts and monorepo tool dependencies
```

## Directory Purposes

**`apps/jquery/`:**
- Purpose: Host and run the interactive demonstration for the jQuery plugin.
- Contains: Express server (`app.js`), template (`views/index.pug`), static assets (`public/**/*`), package-specific scripts (`package.json`).
- Key files: `apps/jquery/app.js`, `apps/jquery/views/index.pug`, `apps/jquery/public/javascripts/demo.js`, `apps/jquery/public/stylesheets/main.styl`.

**`components/jquery/`:**
- Purpose: Implement and package the reusable `@tsparticles/jquery` plugin.
- Contains: TypeScript source (`src/particles.ts`), build outputs (`dist/*`), package build config (`tsconfig.json`, `rollup.config.mjs`), package metadata (`package.json`).
- Key files: `components/jquery/src/particles.ts`, `components/jquery/dist/jquery.particles.js`, `components/jquery/rollup.config.mjs`, `components/jquery/tsconfig.json`.

**`apps/jquery/public/`:**
- Purpose: Serve client-side assets for the demo UI.
- Contains: Browser script (`javascripts/demo.js`), stylesheet sources/outputs (`stylesheets/main.styl`, `stylesheets/main.css`), media (`images/*`, `videos/*`).
- Key files: `apps/jquery/public/javascripts/demo.js`, `apps/jquery/public/stylesheets/main.styl`, `apps/jquery/public/stylesheets/main.css`.

**`apps/jquery/views/`:**
- Purpose: Define server-rendered HTML shell for demo UI.
- Contains: Pug templates.
- Key files: `apps/jquery/views/index.pug`.

**`components/jquery/dist/`:**
- Purpose: Publish-ready artifacts consumed by npm users and demo app static mounts.
- Contains: Unminified/minified JS bundles, sourcemaps, declaration files.
- Key files: `components/jquery/dist/jquery.particles.js`, `components/jquery/dist/jquery.particles.min.js`, `components/jquery/dist/particles.d.ts`.

## Key File Locations

**Entry Points:**
- `components/jquery/src/particles.ts`: Library runtime entry implementing `$.fn.particles`.
- `apps/jquery/app.js`: Demo server process entry.
- `apps/jquery/public/javascripts/demo.js`: Browser runtime entry for demo interactions.

**Configuration:**
- `package.json`: Root monorepo scripts and workspace tool dependencies.
- `pnpm-workspace.yaml`: Workspace package inclusion (`apps/*`, `components/*`).
- `lerna.json`: Lerna package list/versioning strategy.
- `nx.json`: Build cache defaults.
- `components/jquery/tsconfig.json`: Plugin TypeScript compile options.
- `components/jquery/rollup.config.mjs`: Plugin bundle generation config.
- `.github/workflows/nodejs.yml`: CI build workflow.

**Core Logic:**
- `components/jquery/src/particles.ts`: jQuery-to-tsParticles adaptation layer.

**Testing:**
- Not detected: no `*.test.*`, `*.spec.*`, or dedicated test directories in current repository tree.

## Naming Conventions

**Files:**
- Source modules use lowercase descriptive names: `components/jquery/src/particles.ts`.
- Entry scripts use conventional names: `apps/jquery/app.js`, `apps/jquery/public/javascripts/demo.js`.
- Build outputs follow `jquery.particles*` basename pattern in `components/jquery/dist/`.

**Directories:**
- Monorepo package grouping uses plural top-level buckets: `apps/`, `components/`.
- Package directories are lowercase and package-aligned: `apps/jquery/`, `components/jquery/`.
- Static asset subtree uses web-server conventions: `public/javascripts/`, `public/stylesheets/`, `public/images/`, `public/videos/`.

## Where to Add New Code

**New Feature:**
- Primary code: `components/jquery/src/` for plugin API/runtime behavior; `apps/jquery/public/javascripts/` for demo-only UI logic.
- Tests: Not applicable in current structure (no testing harness present); introduce under `components/jquery/src/` co-located or a new `components/jquery/test/` only when adding a test framework.

**New Component/Module:**
- Implementation: Add a new package under `components/<new-package>/` and register it through existing workspace globs in `pnpm-workspace.yaml`/`lerna.json`.

**Utilities:**
- Shared helpers for plugin runtime: place under `components/jquery/src/` and import from `particles.ts`.
- Demo-only helpers: place under `apps/jquery/public/javascripts/`.

## Special Directories

**`.planning/codebase/`:**
- Purpose: Stores generated mapping documents consumed by planning/execution workflows.
- Generated: Yes.
- Committed: Yes.

**`components/jquery/dist/`:**
- Purpose: Compiled/bundled distributable artifacts for package publishing.
- Generated: Yes (via `pnpm run build` in `components/jquery/package.json`).
- Committed: Yes (artifacts present in repository).

**`.nx/`:**
- Purpose: Nx local cache/workspace metadata.
- Generated: Yes.
- Committed: No (ignored by `.gitignore`).

**`node_modules/`:**
- Purpose: Installed dependencies at root and package scopes.
- Generated: Yes.
- Committed: No (ignored by `.gitignore`).

---

*Structure analysis: 2026-04-10*
