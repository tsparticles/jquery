# tsParticles jQuery Modernization (v4 Beta Alignment)

## What This Is

This project modernizes the existing `@tsparticles/jquery` package and demo workspace so they align with the latest tsParticles 4.0.0 beta ecosystem and current JavaScript/TypeScript tooling practices. It focuses on dependency updates, build/runtime compatibility, and syntax modernization while preserving the plugin's existing behavior. The primary users are maintainers and downstream integrators who rely on a stable jQuery wrapper around tsParticles.

## Core Value

Upgrade the jQuery integration to current tsParticles 4.0.0 beta and modern syntax without breaking existing plugin behavior.

## Requirements

### Validated

- ✓ jQuery plugin API exists and initializes particles via `$.fn.particles().init(...)` — existing
- ✓ Demo app serves and exercises plugin behavior in browser with configurable presets — existing
- ✓ Build pipeline produces distributable plugin artifacts (`dist/*.js`, minified bundle, types) — existing
- ✓ Monorepo orchestration supports package-level build flows with pnpm/Lerna/Nx — existing

### Active

- [ ] Upgrade tsParticles-related dependencies to latest 4.0.0 beta versions used by this repository
- [ ] Update supporting dependencies and toolchain packages to current compatible versions
- [ ] Migrate package source/build syntax toward modern TypeScript/JavaScript patterns
- [ ] Preserve runtime compatibility for current jQuery plugin usage and demo behavior
- [ ] Ensure CI/build/lint/test (where available) remain green after migration

### Out of Scope

- New end-user particle features unrelated to modernization — focus is maintenance and migration first
- Full framework rewrite of demo application — unnecessary for this milestone's goal
- Broad API redesign of the jQuery plugin surface — avoid breaking consumers during upgrade

## Context

- Existing brownfield monorepo with mapped codebase documents in `.planning/codebase/`
- Core plugin source is TypeScript-based and wrapped in a jQuery extension model
- Demo app is Express + browser scripts and is used for manual behavior verification
- Project intent (from initialization input): update all packages to latest versions, use repository 4.0.0 beta tsParticles packages, and migrate to more modern syntax

## Constraints

- **Compatibility**: Keep current plugin usage pattern working — avoid regressions for existing consumers
- **Dependency Alignment**: Use tsParticles 4.0.0 beta packages developed in this ecosystem — ensure internal version consistency
- **Build Stability**: Keep package output artifacts and workspace builds functioning after upgrades
- **Scope Control**: Prioritize modernization and reliability over feature expansion

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Treat this initialization as brownfield modernization | Existing package, demo, and build flows are already in active use | — Pending |
| Prioritize dependency/tooling alignment before API changes | Version drift blocks maintenance and increases break risk | — Pending |
| Keep plugin surface stable during v4-beta migration | Reduces integration risk while modernizing internals | — Pending |

---
*Last updated: 2026-04-10 after initialization*
