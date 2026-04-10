# Coding Conventions

**Analysis Date:** 2026-04-10

## Naming Patterns

**Files:**
- Use lowercase file names with feature intent: `components/jquery/src/particles.ts`, `apps/jquery/public/javascripts/demo.js`, `apps/jquery/app.js`.
- Use conventional config names at package root: `components/jquery/.eslintrc.js`, `components/jquery/.prettierrc`, `components/jquery/tsconfig.json`, `components/jquery/rollup.config.mjs`.

**Functions:**
- Use `camelCase` for function names and variables in both TypeScript and JavaScript: `init`, `ajax` in `components/jquery/src/particles.ts`; `updateStats`, `updateParticles` in `apps/jquery/public/javascripts/demo.js`.
- Use function expressions/arrow functions for callbacks and local helpers: `const init = (...) => { ... }` in `components/jquery/src/particles.ts`; `const omit = obj => { ... }` in `apps/jquery/public/javascripts/demo.js`.

**Variables:**
- Use `const` by default and `let` only for mutable values: `const baseId = "tsparticles"` in `components/jquery/src/particles.ts`; `let maxParticles = 0` in `apps/jquery/public/javascripts/demo.js`.
- Use descriptive nouns for DOM references and controls: `cmbPresets`, `btnUpdate`, `element`, `options` in `apps/jquery/public/javascripts/demo.js`.

**Types:**
- Use `PascalCase` for custom type aliases and interfaces: `ParticlesResult`, `IParticlesProps` in `components/jquery/src/particles.ts`.
- Use `I` prefix for exported interface-like aliases intended as public API shape: `IParticlesProps` in `components/jquery/src/particles.ts`.

## Code Style

**Formatting:**
- Tooling is Prettier, configured via package-level Prettier config and shared package preset: `components/jquery/.prettierrc`, `components/jquery/package.json` (`"prettier": "@tsparticles/prettier-config"`).
- Key settings from `components/jquery/.prettierrc`:
  - `printWidth: 120`
  - `endOfLine: "lf"`
  - `tabWidth: 4` override for `*.ts`
- Enforce formatting through scripts in `components/jquery/package.json`:
  - `prettify:src` / `prettify:ci:src`
  - `prettify:readme` / `prettify:ci:readme`

**Linting:**
- Tooling is ESLint with TypeScript parser/plugin in `components/jquery/.eslintrc.js`.
- Base extends in `components/jquery/.eslintrc.js`:
  - `eslint:recommended`
  - `plugin:@typescript-eslint/eslint-recommended`
  - `plugin:@typescript-eslint/recommended`
  - `prettier`
- Key enforced rules in `components/jquery/.eslintrc.js`:
  - `@typescript-eslint/explicit-member-accessibility`: error with `no-public`
  - `@typescript-eslint/no-explicit-any`: warn
  - `@typescript-eslint/no-var-requires`: warn
  - `@typescript-eslint/ban-types`: warn
- Lint scope and ignore policy:
  - Lint command targets `src` via `components/jquery/package.json` (`eslint src --ext .js,.jsx,.ts,.tsx`)
  - Ignore directories in `components/jquery/.eslintignore`: `demo`, `dist`, `node_modules`

## Import Organization

**Order:**
1. Use external package imports first in module files (example: `@tsparticles/engine` in `components/jquery/src/particles.ts`).
2. Use type imports inline with external imports when needed (example: `type Container`, `type ISourceOptions` in `components/jquery/src/particles.ts`).
3. Avoid local relative imports unless feature growth requires splitting modules (not currently present in `components/jquery/src/particles.ts`).

**Path Aliases:**
- No TypeScript path aliases are configured; keep module resolution standard Node-style per `components/jquery/tsconfig.json` (`"moduleResolution": "node"`).
- `baseUrl` and `paths` are not configured in `components/jquery/tsconfig.json`.

## Error Handling

**Patterns:**
- Prefer promise chaining with `.then(...)` for async plugin operations in library code: `tsParticles.load(...).then(callback)` in `components/jquery/src/particles.ts`.
- Validate existence before dereferencing optional runtime objects: `if (container) { ... }` in `apps/jquery/public/javascripts/demo.js`.
- UI/demo-level error handling uses direct user feedback callbacks (`alert(err.toString())`) in `apps/jquery/public/javascripts/demo.js` (`JSONEditor` `onError`).
- No centralized error abstraction is present; keep checks local to the function currently handling external API responses in `components/jquery/src/particles.ts` and `apps/jquery/public/javascripts/demo.js`.

## Logging

**Framework:** console

**Patterns:**
- Use minimal startup logging in server/demo runtime: `console.log(...)` in `apps/jquery/app.js` when app starts.
- Avoid verbose logging in plugin runtime path (`components/jquery/src/particles.ts` has no console logs).

## Comments

**When to Comment:**
- Use concise comments to document intent for plugin API extensions and callback semantics:
  - Global jQuery extension description in `components/jquery/src/particles.ts`
  - Callback intent comments in examples in `components/jquery/README.md`
- Use inline comments for operational constraints in scripts/config where needed:
  - Rate-limit timing comments in `apps/jquery/app.js`

**JSDoc/TSDoc:**
- Use short JSDoc blocks for type/API extension boundaries rather than every function:
  - JSDoc above `ParticlesResult` and `JQuery.particles` declaration in `components/jquery/src/particles.ts`.

## Function Design

**Size:**
- Keep library functions compact and single-purpose; `components/jquery/src/particles.ts` encapsulates plugin behavior in ~46 lines.
- Use nested helper functions for demo orchestration in UI script when interacting with third-party editors/stats (`apps/jquery/public/javascripts/demo.js`).

**Parameters:**
- Prefer explicit typed parameters in TypeScript APIs: `(options: IParticlesProps, callback: (container: Container | undefined) => Promise<void>)` in `components/jquery/src/particles.ts`.
- Use direct event/callback parameters in JavaScript demo code without wrappers when framework already supplies event context (`apps/jquery/public/javascripts/demo.js`).

**Return Values:**
- Return explicit API objects for plugin methods (`return { init, ajax }` in `components/jquery/src/particles.ts`).
- Return object snapshots from metric callbacks when external library expects structured results (`stats.addPanel` callback in `apps/jquery/public/javascripts/demo.js`).

## Module Design

**Exports:**
- Use named type exports for public typing surface (`export type IParticlesProps`) and side-effect module augmentation (`declare global`) in `components/jquery/src/particles.ts`.
- Keep runtime entry as direct jQuery plugin assignment (`$.fn.particles = function () { ... }`) in `components/jquery/src/particles.ts`.

**Barrel Files:**
- Not used in this repository segment; there are no `index.ts` barrel re-export modules under `components/jquery/src/`.

---

*Convention analysis: 2026-04-10*
