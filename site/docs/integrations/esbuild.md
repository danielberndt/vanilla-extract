---
title: esbuild
---

# esbuild

1/ Install the dependencies.

```bash
$ npm install @vanilla-extract/css @vanilla-extract/esbuild-plugin
```

2/ Add the [esbuild](https://esbuild.github.io/) plugin to your build script.

> 💡 This plugin accepts an optional [configuration object](#configuration).

```js
// bundle.js

const {
  vanillaExtractPlugin
} = require('@vanilla-extract/esbuild-plugin');

require('esbuild')
  .build({
    entryPoints: ['app.ts'],
    bundle: true,
    plugins: [vanillaExtractPlugin()],
    outfile: 'out.js'
  })
  .catch(() => process.exit(1));
```

> Please note: There are currently no automatic readable class names during development. However, you can still manually provide a debug ID as the last argument to functions that generate scoped styles, e.g. `export const className = style({ ... }, 'className');`

3/ Process CSS

As [esbuild](https://esbuild.github.io/) currently doesn't have a way to process the CSS generated by vanilla-extract, you can optionally use the `processCss` option.

For example, to run autoprefixer over the generated CSS.

```js
// bundle.js

const {
  vanillaExtractPlugin
} = require('@vanilla-extract/esbuild-plugin');
const postcss = require('postcss');
const autoprefixer = require('autoprefixer');

async function processCss(css) {
  const result = await postcss([autoprefixer]).process(
    css,
    {
      from: undefined /* suppress source map warning */
    }
  );

  return result.css;
}

require('esbuild')
  .build({
    entryPoints: ['app.ts'],
    bundle: true,
    plugins: [
      vanillaExtractPlugin({
        processCss
      })
    ],
    outfile: 'out.js'
  })
  .catch(() => process.exit(1));
```

## Configuration

### identifiers

Different formatting of identifiers (e.g. class names, keyframes, CSS Vars, etc) can be configured by selecting from the following options:

- `short` identifiers are a 7+ character hash. e.g. `hnw5tz3`
- `debug` identifiers contain human readable prefixes representing the owning filename and a potential rule level debug name. e.g. `myfile_mystyle_hnw5tz3`

Each integration will set a default value based on the configuration options passed to the bundler.

### esbuildOptions

esbuild is used internally to compile `.css.ts` files before evaluating them to extract styles. You can pass additional options here to customize that process.
Accepts a subset of esbuild build options (`plugins`, `external`, `define` and `loader`), see https://esbuild.github.io/api/#build-api.