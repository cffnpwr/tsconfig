# @cffnpwr/tsconfig

[![GitHub License](https://img.shields.io/github/license/cffnpwr/tsconfig?style=flat)](./LICENSE)
[![npm Version](https://img.shields.io/npm/v/%40cffnpwr%2Ftsconfig?style=flat)](https://www.npmjs.com/package/@cffnpwr/tsconfig)
[![JSR Version](https://jsr.io/badges/@cffnpwr/tsconfig)](https://jsr.io/@cffnpwr/tsconfig)

Shared TypeScript base configurations for cffnpwr.

[日本語のREADMEはこちら](./README-ja.md)

## Configurations

| File        | Extends     | Adds                | Use for                              |
| ----------- | ----------- | ------------------- | ------------------------------------ |
| `base.json` | –           | strict + module set | Runtime-agnostic base to extend from |
| `bun.json`  | `base.json` | `types: ["bun"]`    | Bun projects                         |
| `node.json` | `base.json` | `types: ["node"]`   | Node.js projects                     |

`base.json` deliberately does not set `types`, `include`, or `exclude`. The environment
variants only add the ambient `types`; everything else is inherited.

## How to Install

### npm

```sh
npm install -D @cffnpwr/tsconfig
```

or

```sh
npx jsr add -D @cffnpwr/tsconfig
```

### pnpm

```sh
pnpm add -D @cffnpwr/tsconfig
```

or

```sh
pnpm dlx jsr add -D @cffnpwr/tsconfig
```

### Bun

```sh
bun add -D @cffnpwr/tsconfig
```

or

```sh
bunx jsr add -D @cffnpwr/tsconfig
```

### Deno

```sh
deno add -D npm:@cffnpwr/tsconfig
```

or

```sh
deno add -D jsr:@cffnpwr/tsconfig
```

## How to Use

Create a `tsconfig.json` and extend the variant that matches your runtime.
`include`/`exclude` are not provided, so set them per project.

Bun:

```jsonc
{
  "extends": "@cffnpwr/tsconfig/bun",
  "include": ["src", "*.config.ts"]
}
```

Node.js:

```jsonc
{
  "extends": "@cffnpwr/tsconfig/node",
  "include": ["src"]
}
```

For any other runtime, extend `base` and set `types` yourself:

```jsonc
{
  "extends": "@cffnpwr/tsconfig/base",
  "compilerOptions": {
    "types": ["@cloudflare/workers-types"]
  },
  "include": ["src"]
}
```

## What `base.json` sets and why

### Module system

| Option                      | Value       | Why                                                                                             |
| --------------------------- | ----------- | ----------------------------------------------------------------------------------------------- |
| `target` / `lib`            | `ESNext`    | Latest syntax and library types; downleveling is the bundler's job.                             |
| `module`                    | `Preserve`  | Keeps `import`/`require` as written and implies `moduleResolution: bundler`.                     |
| `moduleResolution`          | `bundler`   | Resolution model matching Bun/Vite/tsdown. Set explicitly even though `Preserve` implies it.    |
| `moduleDetection`           | `force`     | Treats every non-`.d.ts` file as a module, avoiding accidental global-script scope.             |
| `esModuleInterop`           | `true`      | Interop for CJS default imports (type-checking behavior under `Preserve`).                       |
| `verbatimModuleSyntax`      | `true`      | Forces explicit `import type`; no implicit import elision.                                       |
| `isolatedModules`           | `true`      | Guarantees each file transpiles safely on its own (single-file transpilers, `ts.transpileModule`). |
| `allowImportingTsExtensions`| `true`      | Allows importing `.ts` paths. Requires `noEmit` (or `emitDeclarationOnly`), satisfied below.     |
| `resolveJsonModule`         | `true`      | Allows importing `.json`.                                                                        |

### Emit

| Option                    | Value  | Why                                                                                                       |
| ------------------------- | ------ | --------------------------------------------------------------------------------------------------------- |
| `noEmit`                  | `true` | `tsc` is used for type-checking only; the build is done by a bundler (Bun / tsdown / Vite).               |
| `declaration` / `declarationMap` | `true` | Only take effect when a consumer sets `noEmit: false` to emit `.d.ts` via `tsc`. Under the default `noEmit: true` they emit nothing. Kept so that turning emit on yields declarations without extra config. |

### Strictness

| Option                       | Value  | Why                                                              |
| ---------------------------- | ------ | ---------------------------------------------------------------- |
| `strict`                     | `true` | Full strict family.                                             |
| `noUncheckedIndexedAccess`   | `true` | Adds `undefined` to index-signature access results.            |
| `noImplicitOverride`         | `true` | Requires `override` on overriding members.                     |
| `noImplicitReturns`          | `true` | Flags code paths that don't return a value.                    |
| `noFallthroughCasesInSwitch` | `true` | Flags non-empty `switch` cases that fall through.              |
| `skipLibCheck`               | `true` | Skips type-checking of `.d.ts` files for faster builds.        |

### Intentionally left to the linter

`noUnusedLocals`, `noUnusedParameters`, and `noPropertyAccessFromIndexSignature` are not
set here. Unused-symbol and index-access reporting is handled by
[`@cffnpwr/eslint-config`](https://github.com/cffnpwr/eslint-config), so the type-checker
does not duplicate it.

## License

[MIT License](./LICENSE)
