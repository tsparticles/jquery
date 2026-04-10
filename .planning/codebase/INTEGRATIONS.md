# External Integrations

**Analysis Date:** 2026-04-10

## APIs & External Services

**CDN Distribution:**
- jsDelivr - documented delivery path for browser scripts in `README.md` and `components/jquery/README.md`
  - SDK/Client: Not applicable (script tag CDN usage)
  - Auth: Not applicable

**Community/Project Links:**
- Discord and Telegram links for community access in `README.md` and `components/jquery/README.md`
  - SDK/Client: Not applicable
  - Auth: Not applicable

**Package Ecosystem:**
- npm registry - package publication target for `@tsparticles/jquery` in `components/jquery/package.json`
  - SDK/Client: npm/pnpm CLI via workspace scripts in root `package.json`
  - Auth: Registry auth not stored in repository (no `.npmrc` read; none required for local analysis)

## Data Storage

**Databases:**
- Not detected
  - Connection: Not applicable
  - Client: Not applicable

**File Storage:**
- Local filesystem only (Express static serving from workspace and `node_modules`) in `apps/jquery/app.js`

**Caching:**
- CI dependency caching via GitHub Actions cache for pnpm store in `.github/workflows/nodejs.yml`

## Authentication & Identity

**Auth Provider:**
- None for application runtime
  - Implementation: Demo and plugin do not implement login/auth flows (`apps/jquery/app.js`, `apps/jquery/public/javascripts/demo.js`, `components/jquery/src/particles.ts`)

## Monitoring & Observability

**Error Tracking:**
- None detected (no Sentry/Bugsnag/Rollbar integrations)

**Logs:**
- Console logging only (Express startup log) in `apps/jquery/app.js`

## CI/CD & Deployment

**Hosting:**
- Not explicitly configured for cloud hosting; demo is local Node.js service (`apps/jquery/package.json`)
- Package distribution through npm is configured in `components/jquery/package.json` (`publishConfig.access: public`)

**CI Pipeline:**
- GitHub Actions workflow executes install + `lerna run build:ci` on `main`/`legacy` push and PR in `.github/workflows/nodejs.yml`

## Environment Configuration

**Required env vars:**
- None required for app/plugin runtime detected in code paths (`apps/jquery/app.js`, `components/jquery/src/particles.ts`)
- Optional/commented CI variable: `NX_CLOUD_ACCESS_TOKEN` in `.github/workflows/nodejs.yml`

**Secrets location:**
- GitHub Actions secrets context is referenced for optional Nx Cloud token in `.github/workflows/nodejs.yml`
- `.env*` files: Not detected in repository root during scan

## Webhooks & Callbacks

**Incoming:**
- None detected (Express app exposes only `GET /`) in `apps/jquery/app.js`

**Outgoing:**
- None detected (no outbound HTTP clients or webhook emitters in scanned runtime files)

---

*Integration audit: 2026-04-10*
