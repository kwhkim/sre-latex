# Historical dependency review

Reviewed on 2026-11-07 (Asia/Seoul). Source commit: `93dca77272cf3f413ac4582fd901a59737cf6049` (2022-10-07).

## Scope and selection

Selected the newest releases satisfying the original compatible version ranges that were available by **2023-11-07T23:59:59Z**, then replaced every direct range with an exact version. The originally exact commander dependency remains exact. Added mhchemparser 4.2.1 as an explicit build dependency and removed the nested postinstall installation. The lockfile fixes the complete dependency tree with registry URLs and integrity hashes. `.npmrc` disables automatic lifecycle scripts, defaults to exact saves, and retains the historical resolution cutoff.

All 280 dependency locations (279 distinct name/version pairs) have registry integrity hashes. Registry publication timestamps, retrieved from npm metadata cached during resolution, were checked for all 279 pairs: none were missing or later than the cutoff. Some transitive dependencies necessarily predate 2022; no attempt was made to force incompatible newer leaf dependencies.

## Direct versions

| Package | Version | Published (UTC) |
| --- | --- | --- |
| `speech-rule-engine` | 4.0.7 | 2022-06-27 |
| `commander` | 9.4.0 | 2022-07-15 |
| `@babel/core` | 7.23.2 | 2023-10-12 |
| `@babel/preset-env` | 7.23.2 | 2023-10-11 |
| `babel-loader` | 8.3.0 | 2022-11-03 |
| `mathjax-full` | 3.2.2 | 2022-06-08 |
| `terser-webpack-plugin` | 5.3.9 | 2023-05-17 |
| `webpack` | 5.89.0 | 2023-10-13 |
| `webpack-cli` | 4.10.0 | 2022-06-13 |
| `mhchemparser` | 4.2.1 | 2023-05-24 |

## Reported vulnerabilities

Live `npm audit --json` reports **10 affected package names: 4 high, 5 moderate, 1 low, 0 critical**. Counts are affected packages, not distinct CVEs or reachable vulnerabilities. `npm audit --omit=dev --json` reports **0**. Full JSON responses are saved in `security/`.

| Package | Locked version(s) | Severity | Direct advisory / inherited dependency finding |
| --- | --- | --- | --- |
| `@babel/core` | 7.23.2 | low | [@babel/core: Arbitrary File Read via sourceMappingURL Comment](https://github.com/advisories/GHSA-4x5r-pxfx-6jf8) |
| `@babel/helpers` | 7.23.2 | moderate | [Babel has inefficient RegExp complexity in generated code with .replace when transpiling named capturing groups](https://github.com/advisories/GHSA-968p-4wvh-cqc8) |
| `@babel/plugin-transform-modules-systemjs` | 7.23.0 | high | [@babel/plugin-transform-modules-systemjs generates arbitrary code when compiling malicious input](https://github.com/advisories/GHSA-fv7c-fp4j-7gwp) |
| `@babel/runtime` | 7.23.2 | moderate | [Babel has inefficient RegExp complexity in generated code with .replace when transpiling named capturing groups](https://github.com/advisories/GHSA-968p-4wvh-cqc8) |
| `ajv` | 6.12.6 | moderate | [ajv has ReDoS when using `$data` option](https://github.com/advisories/GHSA-2g4f-4pwh-qvx6) |
| `browserslist` | 4.22.1 | high | [Browserslist: Unbounded memory growth (no cache eviction) via distinct query results, leading to eventual OOM](https://github.com/advisories/GHSA-c83g-rgw3-j3cx); [Browserslist: Uncaught crash / prototype write via untrusted browserslist-stats.json custom stats (normalizeStats)](https://github.com/advisories/GHSA-73wf-gq98-2v4g) |
| `cross-spawn` | 7.0.3 | high | [Regular Expression Denial of Service (ReDoS) in cross-spawn](https://github.com/advisories/GHSA-3xgq-45jj-v275) |
| `serialize-javascript` | 6.0.1 | high | [Serialize JavaScript is Vulnerable to RCE via RegExp.flags and Date.prototype.toISOString()](https://github.com/advisories/GHSA-5c6j-r48x-rmvq); [Serialize JavaScript has CPU Exhaustion Denial of Service via crafted array-like objects](https://github.com/advisories/GHSA-qj8w-gfj5-8c6v); [Cross-site Scripting (XSS) in serialize-javascript](https://github.com/advisories/GHSA-76p7-773f-r4q5); [Serialize JavaScript: Cross-site scripting (XSS) via unescaped </script at the end of a serialized function body](https://github.com/advisories/GHSA-m5mm-p8h7-m29v) |
| `terser-webpack-plugin` | 5.3.9 | moderate | Affected through serialize-javascript |
| `webpack` | 5.89.0 | moderate | [Webpack's AutoPublicPathRuntimeModule has a DOM Clobbering Gadget that leads to XSS](https://github.com/advisories/GHSA-4vvj-4cpr-p986); [webpack buildHttp: allowedUris allow-list bypass via URL userinfo (@) leading to build-time SSRF behavior](https://github.com/advisories/GHSA-8fgc-7cc6-rx7x); [webpack buildHttp HttpUriPlugin allowedUris bypass via HTTP redirects → SSRF + cache persistence](https://github.com/advisories/GHSA-38r7-794h-5758) |

The original webpack 5.73.0 baseline was flagged critical for [CVE-2023-28154](https://github.com/advisories/GHSA-hc6q-2mpp-qw7j). The selected 5.89.0 includes its fix while staying in 2023. Later findings remain; the date constraint excludes some current fixes. No automatic audit fixes or post-2023 overrides were applied.

These findings belong to the development/build dependency graph. Browser DOM-clobbering and HTTP-import SSRF findings have prerequisites that differ from local Node conversion. This checkout does not explicitly configure webpack buildHttp. No exploit-reachability audit was performed. The generated converter bundles MathJax and its dependencies: omitting dev dependencies from npm audit does not independently audit bundled code.

### XML-parser fork caveat

SRE 4.0.7 depends on `xmldom-sre@0.1.31`, an old fork of xmldom. npm audit returns no finding under that package name. Upstream xmldom has [documented parsing/serialization vulnerabilities](https://github.com/xmldom/xmldom/security/advisories/GHSA-h6q6-9hqw-rwfv), and [newer SRE releases](https://github.com/Speech-Rule-Engine/speech-rule-engine/releases) explicitly mention XML-parser security updates. Those reports are not proof that this fork has every upstream defect, but they make the zero runtime audit count insufficient assurance. Applicability and backport status in this fork have not been established. Conversion here supplies MathJax-generated MathML rather than arbitrary user-supplied XML; that narrows input exposure without proving safety.

## Install scripts and malicious-code review limits

The final lockfile has no dependency marked `hasInstallScript`. Installed manifests for the locked package locations were inspected for preinstall/install/postinstall hooks; none are declared. Some packages declare prepare scripts, recorded in the inventory; installation was performed with `--ignore-scripts`. Dependency test fixtures can contain their own package.json files and must not be confused with installed dependency manifests.

The wrapper source, MathJax converter and build configuration, and build-driver source were inspected. No obvious malicious behavior was identified in that limited review. SRE contains dynamic require logic and browser/CDN paths; their presence alone is not evidence of malware. There was no exhaustive manual review of all 280 installed dependency locations, malware detonation, publisher identity verification, or comprehensive network/system-call tracing. npm audit primarily checks reported vulnerabilities and cannot certify absence of malicious code or compromise. Integrity hashes ensure repeatable bytes relative to the lockfile, not trustworthy authorship.

## Verification

- `npm ci --ignore-scripts --no-audit --no-fund` installed all 280 dependency locations from the lockfile.
- Publication timestamps checked for every distinct locked package version; all are at or before the cutoff.
- Explicit `npm run build` completed successfully after the sandbox initially blocked its child process; the successful build log was checked. The upstream build driver can swallow child failures, so exit status alone was not treated as evidence.
- CLI help and English clearspeak conversion passed for powers, fractions, and square roots.
- Module API assertions passed for the exact outputs `x squared`, `a over b`, and `the square root of x`; nested fraction/root and chemistry inputs returned nonempty speech strings.
- Conversion checks ran in the restricted environment without network escalation. This is not a network-access trace or a guarantee that every locale/input works offline.
- Tested runtime: Node.js 24.22.1, npm 11.19.1. No TTS audio generation or historical Node.js runtime test was performed.

## Reproduce

```sh
npm ci --ignore-scripts
npm audit --json
npm audit --omit=dev --json
npm run build
node bin/latex-to-speech --domain clearspeak 'x^2' '\frac{a}{b}'
```

The full audit exits nonzero because known findings remain. Review fixes deliberately if relaxing the historical constraint; do not interpret successful conversion as a clean security result.
