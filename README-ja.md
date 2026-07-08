# @cffnpwr/tsconfig

[![GitHub License](https://img.shields.io/github/license/cffnpwr/tsconfig?style=flat)](./LICENSE)
[![npm Version](https://img.shields.io/npm/v/%40cffnpwr%2Ftsconfig?style=flat)](https://www.npmjs.com/package/@cffnpwr/tsconfig)
[![JSR Version](https://jsr.io/badges/@cffnpwr/tsconfig)](https://jsr.io/@cffnpwr/tsconfig)

cffnpwrのためのTypeScript共通ベース設定です。

[README.md for English is available here](./README.md).

## 設定ファイル

| ファイル    | extends元    | 追加する内容        | 用途                             |
| ----------- | ----------- | ------------------- | -------------------------------- |
| `base.json` | –           | strict + モジュール設定 | 各設定が継承する土台             |
| `bun.json`  | `base.json` | `types: ["bun"]`    | Bunプロジェクト                  |
| `node.json` | `base.json` | `types: ["node"]`   | Node.jsプロジェクト              |

`base.json`は`types`・`include`・`exclude`を意図的に設定しません。環境別バリアントはambientな`types`だけを足し、それ以外はすべて継承します。

## インストール方法

### npm

```sh
npm install -D @cffnpwr/tsconfig
```

または

```sh
npx jsr add -D @cffnpwr/tsconfig
```

### pnpm

```sh
pnpm add -D @cffnpwr/tsconfig
```

または

```sh
pnpm dlx jsr add -D @cffnpwr/tsconfig
```

### Bun

```sh
bun add -D @cffnpwr/tsconfig
```

または

```sh
bunx jsr add -D @cffnpwr/tsconfig
```

### Deno

```sh
deno add -D npm:@cffnpwr/tsconfig
```

または

```sh
deno add -D jsr:@cffnpwr/tsconfig
```

## 使い方

`tsconfig.json`を作成し、ランタイムに合ったバリアントをextendsします。
`include`/`exclude`は提供しないため、プロジェクトごとに設定してください。

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

その他のランタイムでは`base`をextendsし、`types`を自分で指定します。

```jsonc
{
  "extends": "@cffnpwr/tsconfig/base",
  "compilerOptions": {
    "types": ["@cloudflare/workers-types"]
  },
  "include": ["src"]
}
```

## `base.json`が設定する内容と理由

### モジュールシステム

| オプション                    | 値          | 理由                                                                                     |
| --------------------------- | ----------- | ---------------------------------------------------------------------------------------- |
| `target` / `lib`            | `ESNext`    | 最新構文とライブラリ型を使う。ダウンレベルはバンドラの担当。                              |
| `module`                    | `Preserve`  | `import`/`require`を書いたまま保持し、`moduleResolution: bundler`を含意する。            |
| `moduleResolution`          | `bundler`   | Bun/Vite/tsdownに合わせた解決方式。`Preserve`が含意するが明示する。                       |
| `moduleDetection`           | `force`     | `.d.ts`以外の全ファイルをモジュール扱いにし、意図しないグローバルスクリプト化を防ぐ。   |
| `esModuleInterop`           | `true`      | CJSのdefault import互換（`Preserve`下では型チェックの挙動）。                            |
| `verbatimModuleSyntax`      | `true`      | `import type`の明示を強制し、暗黙のimport elisionを無効化する。                          |
| `isolatedModules`           | `true`      | 各ファイルが単体で安全にトランスパイルできることを保証する（単一ファイルトランスパイラ対策）。 |
| `allowImportingTsExtensions`| `true`      | `.ts`拡張子付きimportを許可する。`noEmit`（または`emitDeclarationOnly`）が前提で、下記で満たす。 |
| `resolveJsonModule`         | `true`      | `.json`のimportを許可する。                                                              |

### 出力（emit）

| オプション                  | 値     | 理由                                                                                                          |
| ------------------------- | ------ | ------------------------------------------------------------------------------------------------------------- |
| `noEmit`                  | `true` | `tsc`は型チェック専用とし、ビルドはバンドラ（Bun / tsdown / Vite）が行う。                                    |
| `declaration` / `declarationMap` | `true` | 利用側が`noEmit: false`にして`tsc`で`.d.ts`を出す場合にのみ効く。既定の`noEmit: true`下では何も出力しない。emitを有効化したときに追加設定なしで宣言を得られるよう残している。 |

### 厳格化

| オプション                    | 値     | 理由                                                     |
| ---------------------------- | ------ | -------------------------------------------------------- |
| `strict`                     | `true` | strict系一式を有効化する。                              |
| `noUncheckedIndexedAccess`   | `true` | インデックスシグネチャ経由のアクセス結果に`undefined`を加える。 |
| `noImplicitOverride`         | `true` | オーバーライドするメンバに`override`を必須にする。      |
| `noImplicitReturns`          | `true` | 値を返さないコードパスを検出する。                      |
| `noFallthroughCasesInSwitch` | `true` | フォールスルーする非空の`switch` caseを検出する。       |
| `skipLibCheck`               | `true` | `.d.ts`の型チェックを省略しビルドを高速化する。         |

### 意図的にlinterへ委譲する項目

`noUnusedLocals`・`noUnusedParameters`・`noPropertyAccessFromIndexSignature`はここでは設定しません。未使用シンボルやインデックスアクセスの検出は[`@cffnpwr/eslint-config`](https://github.com/cffnpwr/eslint-config)が担うため、型チェッカーで重複させません。

## License

[MIT License](./LICENSE)
