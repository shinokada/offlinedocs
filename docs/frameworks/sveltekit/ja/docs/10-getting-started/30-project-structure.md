
# プロジェクト構成


一般的な SvelteKit プロジェクトはこのような構成です:

```bash
my-project/
├ src/
│ ├ lib/
│ │ ├ server/
│ │ │ └ [your server-only lib files]
│ │ └ [your lib files]
│ ├ params/
│ │ └ [your param matchers]
│ ├ routes/
│ │ └ [your routes]
│ ├ app.html
│ ├ error.html
│ └ hooks.js
├ static/
│ └ [your static assets]
├ tests/
│ └ [your tests]
├ package.json
├ svelte.config.js
├ tsconfig.json
└ vite.config.js
```

また、`.gitignore`、`.npmrc` などの共通ファイルもあります (もし `npm create svelte@latest` の実行時にオプションを選択した場合は `.prettierrc` や `.eslintrc.cjs` などもあるでしょう)。

## プロジェクトファイル

### src

`src` ディレクトリには、プロジェクトの中身が格納します。

- `lib` にはあなたのライブラリのコードを格納します。格納されたコードは [`$lib`](../50-api-reference/30-modules.html#$lib) エイリアスを使用してインポートしたり、[`svelte-package`](../30-advanced/70-packaging.html) を使用して配布用にパッケージングすることができます。
  - `server` にはあなたのサーバー専用のライブラリのコードを格納します。格納されたコードは [`$lib/server`](../30-advanced/50-server-only-modules.html) エイリアスを使用してインポートすることができます。SvelteKit はこれをクライアントコードにインポートされるのを防ぎます。
- `params` にはアプリに必要な [param matchers](../30-advanced/10-advanced-routing.html-routing#matching) を格納します
- `routes` にはアプリケーションの [ルート(routes)](../20-core-concepts/10-routing.html) を格納します
- `app.html` はページのテンプレートで、以下のプレースホルダーを含む HTML document です:
  - `%sveltekit.head%` — アプリに必要な `<link>` 要素や `<script>` 要素、`<svelte:head>` コンテンツ 
  - `%sveltekit.body%` — レンダリングされるページのためのマークアップです。これを直接 `<body>` の中に置くのではなく、`<div>` または他の要素の中に置く必要があります。ブラウザ拡張(browser extensions)が要素を注入するのをハイドレーションプロセスが破壊してしまう、というバグを防ぐためです。もしこうなっていない場合、SvelteKit は開発中に警告を出します
  - `%sveltekit.assets%` — [`paths.assets`](../50-api-reference/10-configuration.html#paths) が指定されている場合は [`paths.assets`](../50-api-reference/10-configuration.html#paths)、指定されていない場合は [`paths.base`](../50-api-reference/10-configuration.html#paths) への相対パス
  - `%sveltekit.nonce%` — マニュアルで含めるリンクやスクリプトの [CSP](../50-api-reference/10-configuration.html#csp) nonce (使用する場合)
- `error.html` (optional) 全てが失敗したときにレンダリングされるページです。以下のプレースホルダーを含めることができます:
  - `%sveltekit.status%` — HTTP ステータス
  - `%sveltekit.error.message%` — エラーメッセージ
- `hooks.js` (optional) アプリケーションの [hooks](../30-advanced/20-hooks.html)
- `service-worker.js` (optional) [service worker](../30-advanced/40-service-workers.html)

TypeScript を使用している場合、`.js` の代わりに `.ts` ファイルを使用することができます。

### static

`robots.txt` や `favicon.png` など、そのままサーブされる静的なアセットをここに含めます。

### tests

`npm create svelte@latest` の実行時、プロジェクトにテストを追加することを選択した場合、このディレクトリに格納されます。

### package.json

`package.json` ファイルには `@sveltejs/kit`、`svelte`、`vite` が `devDependencies` に含まれていなければなりません。

`npm create svelte@latest` でプロジェクトを作成すると、`package.json` には `"type": "module"` が含まれることに気が付くでしょう。これは、`.js` ファイルが `import` や `export` キーワードを持つネイティブの JavaScript モジュールとして解釈されることを意味します。レガシーな CommonJS ファイルには `.cjs` ファイル拡張子が必要です。

### svelte.config.js

このファイルには Svelte と SvelteKit の [コンフィグレーション](../50-api-reference/10-configuration.html) が含まれています。

### tsconfig.json

`npm create svelte@latest` の際に型チェックを追加した場合、このファイル (または `.ts` ファイルより型チェックされた `.js` ファイルのほうがお好みであれば `jsconfig.json`) で TypeScript の設定を行います。SvelteKit は特定の設定に依存しているため、独自の `.svelte-kit/tsconfig.json` ファイルを生成し、あなたの設定を `extends` (拡張)しています。

### vite.config.js

SvelteKit プロジェクトは実は、[`@sveltejs/kit/vite`](../50-api-reference/30-modules.html#sveltejs-kit-vite) プラグインと、その他の [Vite の設定](https://ja.vitejs.dev/config/) をともに使用した [Vite](https://ja.vitejs.dev) プロジェクトです。

## その他のファイル

### .svelte-kit

開発してプロジェクトをビルドすると、SvelteKit は `.svelte-kit` ディレクトリ ([`outDir`](../50-api-reference/10-configuration.html#outdir) で変更可能です) にファイルを生成します。その中身を気にすることなく、いつでも削除することができます (次に `dev` や `build` を実行したときに再生成されます)。
<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>
