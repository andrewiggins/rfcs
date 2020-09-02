# `createRoot`

## Replacing `options`

```js
// preact/compat
// Hmm can't provide default implementation... (e.g. catchError)
function extend(createRoot) {
  return (container, options) => {
    return createRoot(container, {
      diff(...args) {
        console.log("Before diff");
        options.diff(...args);
        console.log("After diff");
      },
    });
  };
}

// index.js - sample user code

import { createRoot as preactCreateRoot } from "preact";
import { extend as compat } from "preact/compat";
import { extend as debug } from "preact/debug";

let createRoot = compat(preactCreateRoot);

// No longer re-enforce old NodeJS conventions in browser code
// Allow user to determine how they want to
if (DEBUG_MODE) {
  createRoot = debug(createRoot);
}

export { createRoot };
```

## Rollup style plugin list

May not work well for options that need to do work before and after base
implementation is called. Rollup plugins aren't a middleware-like pattern.

```js
// preact/compat
import { createRoot as preactCreateRoot } from "preact";

const compat = {
  diff(...args) {},
  diffed(...args) {},
};

function createRoot(container, plugins) {
  return preactCreateRoot(container, [...plugins, compat]);
}

export {
  compat, // For custom plugin integration
  createRoot, // For webpack/rollup aliasing
};

// index.js - sample user code
import { createRoot } from "preact";
import { compat } from "preact/compat";
import { debug } from "preact/debug";

// Unnecessary if user imports createRoot directly from "preact/compat"
let plugins = [compat];

if (DEBUG_MODE) {
  plugins.push(debug);
}

let root = createRoot(document.body, { plugins });
root.render(<div>Hello World</div>);
```

## Back-compat options

Might be somewhat possible if `createRoot` mixes the global options
with the passed in options.
