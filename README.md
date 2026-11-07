# LaTeX to Speech Conversion

Generates speech output for LaTeX expressions. This is based on
[MathJax](https://mathjax.org) and [Speech Rule
Engine](https://speechruleengine.org).


## Pinned historical checkout

This checkout pins compatible direct dependency releases from 2022-2023 and
locks all transitive dependencies to releases available by 2023-11-07 UTC.
The lockfile includes older leaf packages where required by those dependencies.
See [DEPENDENCY_REVIEW.md](DEPENDENCY_REVIEW.md) for known vulnerabilities and
review limits. These historical pins are for compatibility, not a security guarantee.

To build this checkout locally (without fetching the separately published wrapper):

```sh
npm ci --ignore-scripts
npm run build
node bin/latex-to-speech --domain clearspeak 'x^2' '\frac{a}{b}'
```

Installation hooks are disabled in `.npmrc`. The build is an explicit execution
step and still runs with that setting. `mhchemparser` is a pinned build dependency;
there is no nested postinstall download. Keep `package-lock.json` under version
control and use `npm ci` to preserve the reviewed tree. The generated
`bin/tex2mml.js` and `node_modules/` are ignored by Git. Build before using the module.
The build and conversion examples were checked with Node.js 24.22.1 and npm 11.19.1;
this does not establish compatibility with all older Node.js versions.

The upstream package installation instructions below refer to the public npm
package, which does not acquire this checkout's dependency pins.

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
