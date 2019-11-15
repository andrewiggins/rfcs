# `createRoot`

## Replacing `options`

```js
// Sample user code

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

## Back-compat options

Might be somewhat possible if `createRoot` mixes the global options
with the passed in options.
