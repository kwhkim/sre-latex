# Exact dependency pin review

Updated on 2026-11-07 (Asia/Seoul). Supersedes the historical 2023 cutoff review.

## Requested versions

| Package | Exact version |
| --- | --- |
| `@babel/plugin-transform-modules-systemjs` | 7.29.4 |
| `@babel/preset-env` | 7.29.5 |
| `browserslist` | 4.28.7 |
| `cross-spawn` | 7.0.5 |
| `serialize-javascript` | 7.1.3 |
| `@babel/helpers` | 7.29.2 |
| `@babel/runtime` | 7.26.10 |
| `ajv` | 6.14.0 |
| `terser-webpack-plugin` | 5.6.1 |
| `webpack` | 5.104.1 |

All ten are explicit development dependencies so each requested package is represented in the lockfile, even when newer parent packages no longer require it. Overrides prevent nested copies from resolving to different versions, except for the separate Ajv major-version branch described below. The wrappers runtime dependencies and unrelated direct build dependency pins remain unchanged.

## Babel core update

`@babel/core` is now pinned to **7.29.6**. It requires `@babel/helpers@^7.29.2`; the previous forced 7.26.10 pin caused `helpers(...).isInternal is not a function` during the build. Helpers is therefore pinned to exact **7.29.2**. All other direct package pins remain unchanged. Clean installation, full dependency-tree validation, the inspected build log, and power/fraction/root/nested/chemistry conversion checks passed with Node.js 24.22.1.

## Compatibility adjustment

Forcing Ajv 6 globally broke the new terser-webpack-plugin build: schema-utils/ajv-keywords requires `ajv/dist/compile/codegen`, an Ajv 8 interface. The final overrides scope Ajv 6 consumers to the requested **6.14.0** and Ajv 8 consumers to exact **8.18.0**. This is an additional compatibility dependency, not a replacement for the requested Ajv 6 pin. The build and dependency-tree checks pass with both branches.

The old `.npmrc` `before=2023-11-07` setting has been removed because it conflicts with these requested 2024-2026 updates. `save-exact=true` and `ignore-scripts=true` remain enabled. Automatic package installation hooks are still disabled.

## Current audit results

- Full dependency audit: **0 low, 0 moderate, 0 high, 0 critical**.
- The previous Babel core source-map finding is no longer reported after the 7.29.6 update.
- Runtime-only audit: **0 reported findings**. This does not independently audit bundled code or prove safety of old parser forks.
- Current full audit: `security/npm-audit-core-7.29.6-2026-11-07.json`. Earlier exact-pins audit reports describe the tree before this Babel core update.
- Pre-core-update installed-package inventory: `security/package-inventory-exact-pins-2026-11-07.json`.

## Verification

- Clean temporary installation from the final lockfile: `npm ci --ignore-scripts`, 267 dependency locations.
- Asserted every requested package version in the lockfile, including nested copies; the only additional Ajv version is the documented 8.18.0 branch.
- `npm ls --all --json` passed with no invalid dependency-tree errors.
- Explicit `npm run build` completed; the build log was inspected for errors, and the generated converter was exercised. The upstream makeAll driver can return success after an internal failure, so exit status alone was not accepted.
- Module API assertions passed for `x squared`, `a over b`, and `the square root of x`; nested fraction/root and chemistry inputs returned nonempty strings.
- Tested with Node.js 24.22.1 and npm 11.19.1. No TTS audio generation, Docker build, or older Node.js compatibility test was performed.

## Review limits and historical evidence

The updated dependencies have not received an exhaustive manual malicious-code audit. Known vulnerability reports and working conversions do not certify absence of compromise. The existing `xmldom-sre@0.1.31` fork concern remains unresolved and is not covered by the runtime audit count.

The earlier historical-tree review is preserved as `security/DEPENDENCY_REVIEW-historical-2026-11-07.md`. The older audit and inventory files without `exact-pins` in their names describe the superseded dependency tree; they are retained as historical evidence and must not be read as current results.

## Reproduce

```sh
npm ci --ignore-scripts
npm audit --json
npm audit --omit=dev --json
npm run build
node bin/latex-to-speech --domain clearspeak 'x^2' '\frac{a}{b}'
```

The current full audit exits successfully with zero reported findings. This is not proof that the complete dependency tree is free of vulnerabilities or malicious code.
