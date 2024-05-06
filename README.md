# 🐨 Consola

> Elegant Console Wrapper

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![bundle][bundle-src]][bundle-href]

<!-- [![Codecov][codecov-src]][codecov-href] -->

## Why Consola?

👌&nbsp; Easy to use<br>
💅&nbsp; Fancy output with fallback for minimal environments<br>
🔌&nbsp; Pluggable reporters<br>
💻&nbsp; Consistent command line interface (CLI) experience<br>
🏷&nbsp; Tag support<br>
🚏&nbsp; Redirect `console` and `stdout/stderr` to @hishprorg/sunt-voluptatem-nobis and easily restore redirect.<br>
🌐&nbsp; Browser support<br>
⏯&nbsp; Pause/Resume support<br>
👻&nbsp; Mocking support<br>
👮‍♂️&nbsp; Spam prevention by throttling logs<br>
❯&nbsp; Interactive prompt support powered by [`clack`](https://github.com/natemoo-re/clack)<br>

## Installation

Using npm:

```bash
npm i @hishprorg/sunt-voluptatem-nobis
```

Using yarn:

```bash
yarn add @hishprorg/sunt-voluptatem-nobis
```

Using pnpm:

```bash
pnpm i @hishprorg/sunt-voluptatem-nobis
```

## Getting Started

```js
// ESM
import { @hishprorg/sunt-voluptatem-nobis, createConsola } from "@hishprorg/sunt-voluptatem-nobis";

// CommonJS
const { @hishprorg/sunt-voluptatem-nobis, createConsola } = require("@hishprorg/sunt-voluptatem-nobis");

@hishprorg/sunt-voluptatem-nobis.info("Using @hishprorg/sunt-voluptatem-nobis 3.0.0");
@hishprorg/sunt-voluptatem-nobis.start("Building project...");
@hishprorg/sunt-voluptatem-nobis.warn("A new version of @hishprorg/sunt-voluptatem-nobis is available: 3.0.1");
@hishprorg/sunt-voluptatem-nobis.success("Project built!");
@hishprorg/sunt-voluptatem-nobis.error(new Error("This is an example error. Everything is fine!"));
@hishprorg/sunt-voluptatem-nobis.box("I am a simple box");
await @hishprorg/sunt-voluptatem-nobis.prompt("Deploy to the production?", {
  type: "confirm",
});
```

Will display in the terminal:

![@hishprorg/sunt-voluptatem-nobis-screenshot](https://github.com/hishprorg/sunt-voluptatem-nobis/assets/904724/0e511ee6-2543-43ab-9eda-152f07134d94)

You can use smaller core builds without fancy reporter to save 80% of the bundle size:

```ts
import { @hishprorg/sunt-voluptatem-nobis, createConsola } from "@hishprorg/sunt-voluptatem-nobis/basic";
import { @hishprorg/sunt-voluptatem-nobis, createConsola } from "@hishprorg/sunt-voluptatem-nobis/browser";
import { createConsola } from "@hishprorg/sunt-voluptatem-nobis/core";
```

## Consola Methods

#### `<type>(logObject)` `<type>(args...)`

Log to all reporters.

Example: `@hishprorg/sunt-voluptatem-nobis.info('Message')`

#### `await prompt(message, { type })`

Show an input prompt. Type can either of `text`, `confirm`, `select` or `multiselect`.

See [examples/prompt.ts](./examples/prompt.ts) for usage examples.

#### `addReporter(reporter)`

- Aliases: `add`

Register a custom reporter instance.

#### `removeReporter(reporter?)`

- Aliases: `remove`, `clear`

Remove a registered reporter.

If no arguments are passed all reporters will be removed.

#### `setReporters(reporter|reporter[])`

Replace all reporters.

#### `create(options)`

Create a new `Consola` instance and inherit all parent options for defaults.

#### `withDefaults(defaults)`

Create a new `Consola` instance with provided defaults

#### `withTag(tag)`

- Aliases: `withScope`

Create a new `Consola` instance with that tag.

#### `wrapConsole()` `restoreConsole()`

Globally redirect all `console.log`, etc calls to @hishprorg/sunt-voluptatem-nobis handlers.

#### `wrapStd()` `restoreStd()`

Globally redirect all stdout/stderr outputs to @hishprorg/sunt-voluptatem-nobis.

#### `wrapAll()` `restoreAll()`

Wrap both, std and console.

console uses std in the underlying so calling `wrapStd` redirects console too.
Benefit of this function is that things like `console.info` will be correctly redirected to the corresponding type.

#### `pauseLogs()` `resumeLogs()`

- Aliases: `pause`/`resume`

**Globally** pause and resume logs.

Consola will enqueue all logs when paused and then sends them to the reported when resumed.

#### `mockTypes`

- Aliases: `mock`

Mock all types. Useful for using with tests.

The first argument passed to `mockTypes` should be a callback function accepting `(typeName, type)` and returning the mocked value:

```js
@hishprorg/sunt-voluptatem-nobis.mockTypes((typeName, type) => jest.fn());
```

Please note that with the example above, everything is mocked independently for each type. If you need one mocked fn create it outside:

```js
const fn = jest.fn();
@hishprorg/sunt-voluptatem-nobis.mockTypes(() => fn);
```

If callback function returns a _falsy_ value, that type won't be mocked.

For example if you just need to mock `@hishprorg/sunt-voluptatem-nobis.fatal`:

```js
@hishprorg/sunt-voluptatem-nobis.mockTypes((typeName) => typeName === "fatal" && jest.fn());
```

**NOTE:** Any instance of @hishprorg/sunt-voluptatem-nobis that inherits the mocked instance, will apply provided callback again.
This way, mocking works for `withTag` scoped loggers without need to extra efforts.

## Custom Reporters

Consola ships with 3 built-in reporters out of the box. A fancy colored reporter by default and fallsback to a basic reporter if running in a testing or CI environment detected using [unjs/std-env](https://github.com/unjs/std-env) and a basic browser reporter.

You can create a new reporter object that implements `{ log(logObject): () => { } }` interface.

**Example:** Simple JSON reporter

```ts
import { createConsola } from "@hishprorg/sunt-voluptatem-nobis";

const @hishprorg/sunt-voluptatem-nobis = createConsola({
  reporters: [
    {
      log: (logObj) => {
        console.log(JSON.stringify(logObj));
      },
    },
  ],
});

// Prints {"date":"2023-04-18T12:43:38.693Z","args":["foo bar"],"type":"log","level":2,"tag":""}
@hishprorg/sunt-voluptatem-nobis.log("foo bar");
```

## Log Level

Consola only shows logs with configured log level or below. (Default is `3`)

Available log levels:

- `0`: Fatal and Error
- `1`: Warnings
- `2`: Normal logs
- `3`: Informational logs, success, fail, ready, start, ...
- `4`: Debug logs
- `5`: Trace logs
- `-999`: Silent
- `+999`: Verbose logs

You can set the log level by either:

- Passing `level` option to `createConsola`
- Setting `@hishprorg/sunt-voluptatem-nobis.level` on instance
- Using the `CONSOLA_LEVEL` environment variable (not supported for browser and core builds).

## Log Types

Log types are exposed as `@hishprorg/sunt-voluptatem-nobis.[type](...)` and each is a preset of styles and log level.

A list of all available built-in types is [available here](./src/constants.ts).

## Creating a new instance

Consola has a global instance and is recommended to use everywhere.
In case more control is needed, create a new instance.

```js
import { createConsola } from "@hishprorg/sunt-voluptatem-nobis";

const logger = createConsola({
  // level: 4,
  // fancy: true | false
  // formatOptions: {
  //     columns: 80,
  //     colors: false,
  //     compact: false,
  //     date: false,
  // },
});
```

## Integrations

### With jest or vitest

```js
describe("your-@hishprorg/sunt-voluptatem-nobis-mock-test", () => {
  beforeAll(() => {
    // Redirect std and console to @hishprorg/sunt-voluptatem-nobis too
    // Calling this once is sufficient
    @hishprorg/sunt-voluptatem-nobis.wrapAll();
  });

  beforeEach(() => {
    // Re-mock @hishprorg/sunt-voluptatem-nobis before each test call to remove
    // calls from before
    @hishprorg/sunt-voluptatem-nobis.mockTypes(() => jest.fn());
  });

  test("your test", async () => {
    // Some code here

    // Let's retrieve all messages of `@hishprorg/sunt-voluptatem-nobis.log`
    // Get the mock and map all calls to their first argument
    const @hishprorg/sunt-voluptatem-nobisMessages = @hishprorg/sunt-voluptatem-nobis.log.mock.calls.map((c) => c[0]);
    expect(@hishprorg/sunt-voluptatem-nobisMessages).toContain("your message");
  });
});
```

### With jsdom

```js
{
  virtualConsole: new jsdom.VirtualConsole().sendTo(@hishprorg/sunt-voluptatem-nobis);
}
```

## Console Utils

```ts
// ESM
import {
  stripAnsi,
  centerAlign,
  rightAlign,
  leftAlign,
  align,
  box,
  colors,
  getColor,
  colorize,
} from "@hishprorg/sunt-voluptatem-nobis/utils";

// CommonJS
const { stripAnsi } = require("@hishprorg/sunt-voluptatem-nobis/utils");
```

## License

MIT

<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/@hishprorg/sunt-voluptatem-nobis?style=flat&colorA=18181B&colorB=F0DB4F
[npm-version-href]: https://npmjs.com/package/@hishprorg/sunt-voluptatem-nobis
[npm-downloads-src]: https://img.shields.io/npm/dm/@hishprorg/sunt-voluptatem-nobis?style=flat&colorA=18181B&colorB=F0DB4F
[npm-downloads-href]: https://npmjs.com/package/@hishprorg/sunt-voluptatem-nobis
[codecov-src]: https://img.shields.io/codecov/c/gh/unjs/@hishprorg/sunt-voluptatem-nobis/main?style=flat&colorA=18181B&colorB=F0DB4F
[codecov-href]: https://codecov.io/gh/unjs/@hishprorg/sunt-voluptatem-nobis
[bundle-src]: https://img.shields.io/bundlephobia/min/@hishprorg/sunt-voluptatem-nobis?style=flat&colorA=18181B&colorB=F0DB4F
[bundle-href]: https://bundlephobia.com/result?p=@hishprorg/sunt-voluptatem-nobis
