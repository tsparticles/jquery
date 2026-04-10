# Technology Stack

**Analysis Date:** 2026-04-10

## Languages

**Primary:**
- TypeScript - plugin source in `components/jquery/src/particles.ts` and TypeScript build config in `components/jquery/tsconfig.json`
- JavaScript (Node.js + browser) - demo server and frontend scripts in `apps/jquery/app.js` and `apps/jquery/public/javascripts/demo.js`

**Secondary:**
- Pug templates - demo UI markup in `apps/jquery/views/index.pug`
- Stylus/CSS - demo styling in `apps/jquery/public/stylesheets/main.styl` and generated CSS in `apps/jquery/public/stylesheets/main.css`
- YAML/JSON - workspace and automation config in `pnpm-workspace.yaml`, `.github/workflows/nodejs.yml`, `nx.json`, and `lerna.json`

## Runtime

**Environment:**
- Node.js (explicitly pinned to `16` in CI workflow) in `.github/workflows/nodejs.yml`
- Browser runtime for plugin/demo via jQuery + tsParticles in `apps/jquery/views/index.pug` and `apps/jquery/public/javascripts/demo.js`

**Package Manager:**
- pnpm (workspace manager pinned in root manifest) in `package.json` (`packageManager: pnpm@10.33.0...`)
- Lockfile: present (`pnpm-lock.yaml`)

## Frameworks

**Core:**
- tsParticles engine (`@tsparticles/engine`) - particle engine consumed by jQuery wrapper in `components/jquery/src/particles.ts`
- jQuery plugin model - extension via `$.fn.particles` in `components/jquery/src/particles.ts`
- Express - demo web app server in `apps/jquery/app.js`

**Testing:**
- Not detected (no Jest/Vitest/Mocha config files in repository root or package-level manifests)

**Build/Dev:**
- TypeScript (`tsc`) - compile plugin sources (`components/jquery/package.json`, `components/jquery/tsconfig.json`)
- Rollup (`rollup -c`) - bundle plugin artifact (`components/jquery/rollup.config.mjs`)
- Babel (`@rollup/plugin-babel`, `@babel/preset-env`) - transpilation stage in `components/jquery/rollup.config.mjs` and `components/jquery/.babelrc`
- UglifyJS - minification step in `components/jquery/package.json`
- Lerna + Nx + pnpm workspaces - monorepo orchestration in `package.json`, `lerna.json`, `nx.json`, and `pnpm-workspace.yaml`
- Stylus - demo CSS build in `apps/jquery/package.json` (`build:style`)

## Key Dependencies

**Critical:**
- `@tsparticles/engine` - core rendering/runtime API for plugin behavior in `components/jquery/src/particles.ts`
- `jquery` - required peer/runtime dependency for plugin extension in `components/jquery/package.json` and `components/jquery/src/particles.ts`
- `@tsparticles/jquery` - local workspace package consumed by demo app in `apps/jquery/package.json`
- `tsparticles` - demo bundle loaded in browser in `apps/jquery/views/index.pug`

**Infrastructure:**
- `express` - local demo hosting in `apps/jquery/app.js`
- `helmet` and `express-rate-limit` - security middleware dependencies declared in `apps/jquery/package.json` (present but currently commented out in `apps/jquery/app.js`)
- `eslint` + `@typescript-eslint/*` + `prettier` - quality tooling in `components/jquery/.eslintrc.js`, `components/jquery/.prettierrc`, and package manifests
- `husky` + `@commitlint/*` - commit workflow tooling in root `package.json` and `.husky/commit-msg`

## Configuration

**Environment:**
- Runtime configuration is code-driven (no active `.env*` files detected in repository root)
- Optional CI secret reference exists for Nx Cloud token in commented workflow env block in `.github/workflows/nodejs.yml` (`NX_CLOUD_ACCESS_TOKEN`)

**Build:**
- Workspace/build orchestration: `package.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`
- Package build configs: `components/jquery/tsconfig.json`, `components/jquery/rollup.config.mjs`, `components/jquery/.babelrc`, `components/jquery/.eslintrc.js`, `components/jquery/.prettierrc`
- CI build pipeline: `.github/workflows/nodejs.yml`

## Platform Requirements

**Development:**
- Node.js + pnpm required to install and build workspace packages (`package.json`, `pnpm-lock.yaml`)
- Browser required for interactive demo runtime (`apps/jquery/views/index.pug`, `apps/jquery/public/javascripts/demo.js`)

**Production:**
- Primary production target is npm package distribution for `@tsparticles/jquery` (`components/jquery/package.json`)
- Demo app can run as a Node.js Express service via `node ./app.js` in `apps/jquery/package.json`

---

*Stack analysis: 2026-04-10*
