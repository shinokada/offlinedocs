
# SvelteKit で X を使うにはどうすればよいですか？


[ドキュメントのインテグレーションのセクション](../60-appendix/20-additional-resources.html#integrations) をしっかり読み込んでください。それでも問題が解決しない場合のために、よくある問題の解決策を以下に示します。

## データベースのセットアップはどう行えばよいですか？

データベースに問い合わせを行うコードを [サーバールート(server route)](../20-core-concepts/10-routing.html#server) に置いてください。.svelte ファイルの中でデータベースに問い合わせを行わないでください。コネクションをすぐにセットアップし、シングルトンとしてアプリ全体からクライアントにアクセスできるように `db.js` のようなものを作ることができます。`hooks.js` で1回セットアップするコードを実行し、データベースヘルパーを必要とするすべてのエンドポイントにインポートできます。

## ミドルウェア(middleware)を使うにはどうすればよいですか？

`adapter-node` は、プロダクションモードで使用するためのミドルウェアを自分のサーバで構築します。開発モードでは、Vite プラグインを使用して Vite にミドルウェア(middleware) を追加することができます。例えば:

```js
// @filename: ambient.d.ts
declare module '@sveltejs/kit/vite'; // TODO this feels unnecessary, why can't it 'see' the declarations?

// @filename: index.js
// cut---
import { sveltekit } from '@sveltejs/kit/vite';

/** @type {import('vite').Plugin} */
const myPlugin = {
	name: 'log-request-middleware',
	configureServer(server) {
		server.middlewares.use((req, res, next) => {
			console.log(`Got request ${req.url}`);
			next();
		});
	}
};

/** @type {import('vite').UserConfig} */
const config = {
	plugins: [myPlugin, sveltekit()]
};

export default config;
```

順序を制御する方法など、詳しくは [Vite の `configureServer` のドキュメント](https://ja.vitejs.dev/guide/api-plugin.html#configureserver) をご覧ください。

## `document` や `window` に依存しているクライアントサイドオンリーなライブラリはどう使えばよいですか？

もし `document` や `window` 変数にアクセスする必要があったり、クライアントサイドだけで実行するコードが必要な場合は、`browser` チェックでラップしてください:

```js
/// <reference types="@sveltejs/kit" />
// cut---
import { browser } from '$app/environment';

if (browser) {
	// client-only code here
}
```

コンポーネントが最初にDOMにレンダリングされた後にコードを実行したい場合は、`onMount` で実行することもできます:

```js
// @filename: ambient.d.ts
// @lib: ES2015
declare module 'some-browser-only-library';

// @filename: index.js
// cut---
import { onMount } from 'svelte';

onMount(async () => {
	const { method } = await import('some-browser-only-library');
	method('hello world');
});
```

使用したいライブラリに副作用がなければ静的にインポートすることができますし、サーバー側のビルドでツリーシェイクされ、`onMount` が自動的に no-op に置き換えられます:

```js
// @filename: ambient.d.ts
// @lib: ES2015
declare module 'some-browser-only-library';

// @filename: index.js
// cut---
import { onMount } from 'svelte';
import { method } from 'some-browser-only-library';

onMount(() => {
	method('hello world');
});
```

一方、ライブラリに副作用があっても静的にインポートをしたい場合は、[vite-plugin-iso-import](https://github.com/bluwy/vite-plugin-iso-import) をチェックして `?client` インポートサフィックスをサポートしてください。このインポートは SSR ビルドでは取り除かれます。しかし、この手法を使用すると VS Code Intellisense が使用できなくなることにご注意ください。

```js
// @filename: ambient.d.ts
// @lib: ES2015
declare module 'some-browser-only-library?client';

// @filename: index.js
// cut---
import { onMount } from 'svelte';
import { method } from 'some-browser-only-library?client';

onMount(() => {
	method('hello world');
});
```

## Yarn を使用するにはどうすれば良いですか？

### Yarn 2 で動作しますか？

多少は。Plug'n'Play 機能、通称 'pnp' は動きません (Node のモジュール解決アルゴリズムから逸脱しており、SvelteKitが[数多くのライブラリ](https://blog.sindresorhus.com/get-ready-for-esm-aa53530b3f77)とともに使用している[ネイティブの JavaScript モジュールではまだ動作しません](https://github.com/yarnpkg/berry/issues/638))。[`.yarnrc.yml`](https://yarnpkg.com/configuration/yarnrc#nodeLinker) で `nodeLinker: 'node-modules'` を使用して pnp を無効にできますが、おそらく npm や [pnpm](https://pnpm.io/) を使用するほうが簡単でしょう。同じように高速で効率的ですが、互換性に頭を悩ませることはありません。

### Yarn 3 を使用するにはどうすれば良いですか？

現時点の、最新の Yarn (version 3) の ESM サポート は [experimental](https://github.com/yarnpkg/berry/pull/2161) であるようです。

結果は異なるかもしれませんが、下記が有効なようです。

最初に新しいアプリケーションを作成します:

```sh
yarn create svelte myapp
cd myapp
```

そして Yarn Berry を有効にします:

```sh
yarn set version berry
yarn install
```

**Yarn 3 global cache**

Yarn Berry の興味深い機能の1つに、ディスク上のプロジェクトごとに複数のコピーを持つのではなく、パッケージ用に単一のグローバルキャッシュを持つことができる、というのがあります。しかし、`enableGlobalCache` の設定を true にするとビルドが失敗するため、`.yarnrc.yml` ファイルに以下を追加することを推奨します:

```
nodeLinker: node-modules
```

これによってパッケージはローカルの node_modules ディレクトリにダウンロードされますが、上記の問題は回避され、現時点では Yarn の version 3 を使用するベストな方法となります。
<span style='float: footnote;'><a href="../index.html#toc">Go to TOC</a></span>
