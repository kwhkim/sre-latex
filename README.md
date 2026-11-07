# LaTeX to Speech Conversion

Generates speech output for LaTeX expressions. This is based on
[MathJax](https://mathjax.org) and [Speech Rule
Engine](https://speechruleengine.org).


## Pinned checkout

This checkout locks the ten explicitly requested security-update versions in
`package.json` and `package-lock.json`. Direct dependencies use exact versions;
root overrides enforce the requested versions throughout the dependency tree.
Ajv 6 consumers use 6.14.0, while consumers requiring Ajv 8 use a separate exact
8.18.0 pin. Both branches are necessary for the build.

The old 2023 resolution cutoff has been removed. Existing compatible pins are
retained where possible; dependencies required by the updated tools may be newer.
See [DEPENDENCY_REVIEW.md](DEPENDENCY_REVIEW.md) for current audit results,
verification, and remaining limitations.

Build this checkout locally:

```sh
npm ci --ignore-scripts
npm run build
node bin/latex-to-speech --domain clearspeak 'x^2' '\frac{a}{b}'
```

Installation hooks remain disabled in `.npmrc`; the explicit build still runs.
`mhchemparser` is pinned as a build dependency, with no nested postinstall download.
Keep the lockfile under version control and use `npm ci`. The generated
`bin/tex2mml.js` and `node_modules/` are ignored by Git. Build before using the module.

The build and conversions were checked with Node.js 24.22.1 and npm 11.19.1;
compatibility with historical Node.js versions was not tested. In particular,
new dependencies can require a newer Node.js runtime.

The upstream installation instructions below refer to the public npm package,
which does not acquire this checkout's pins or root overrides.

## Install

Install with

``` shell
npm install latex-to-speech
```

## Run on CLI

``` shell
npx latex-to-speech [options] <expr...>
```

or

``` shell
[PATH-TO-NODE_MODULES]/node_modules/.bin/latex-to-speech [options] <expr...>
```

Expressions are a list of LaTeX formulas. For options, see the options
below. For more detailed explanations see also the documentation of [Speech Rule
Engine](https://github.com/Speech-Rule-Engine/speech-rule-engine/).


## Use as Module

Loading the module returns the `latexToSpeech` function.

``` javascript
const latexToSpeech = require('latex-to-speech');
```

`latexToSpeech` takes a list of LaTeX expressions and a feature vector of
options as input. It is an async function that returns a promise for the list of
results. Here are some examples:

``` javascript
let result = await latexToSpeech(['x^2', '\\frac{a}{b}'], {});
```

will result in `[ 'x squared', 'StartFraction a Over b EndFraction' ]`.

``` javascript
let result = await latexToSpeech(['x^2', '\\frac{a}{b}'], {domain: 'clearspeak'});
```

will result in `[ 'x squared', 'a over b' ]`.



## Options

Options for `latex-to-speech` are a subset of the Speech Rule Engine options.
 For more detailed explanations see also [the documentation
 there](https://github.com/Speech-Rule-Engine/speech-rule-engine/).


| Option     | Value                                                                                                          |
|------------|----------------------------------------------------------------------------------------------------------------|
| *domain*   | Domain or subject area of speech rules (e.g., mathspeak, clearspeak).                                          |
| *style*    | Style or preference setting of speech rules (e.g., brief).                                                     |
|            | In case of clearspeak, multiple preferences can be chosen using `:` as separator.                              |
| *locale*   | Language locale in 639-1.                                                                                      |
| *markup*   | Set output markup for speech: ```none```, ```ssml```, ```sable```, ```voicexml```, ```acss```, ```ssml_step``` |
| *modality* | Set the modality SRE returns. E.g., ```speech```, ```braille```, ```prefix```, ```summary```                   |


## Build

Building from github:

``` shell
npm install; npm run build;
```
