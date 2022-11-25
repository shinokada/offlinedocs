
# Packaging


> `svelte-package` は現時点では experimental です。将来のリリースで後方互換性のない変更が行われる可能性があります。

SvelteKit では、アプリを構築するだけでなく、`@sveltejs/package` パッケージを使用してコンポーネントライブラリを構築することもできます (`npm create svelte` にはこれを設定するためのオプションがあります)。

アプリを作成するとき、`src/routes` のコンテンツが公開される部分となります。[`src/lib`](../50-api-reference/30-modules.html#$lib) にはアプリの内部ライブラリが含まれます。

コンポーネントライブラリは、SvelteKitアプリと全く同じ構造を持ちますが、`src/lib` も公開される点が異なります。`src/routes` はライブラリに付随するドキュメントやデモサイトにもできますし、開発中に使用できるサンドボックスにもできます。

`@sveltejs/package` の `svelte-package` コマンドを実行すると、`src/lib` のコンテンツを使用して、以下を含む `package` ディレクトリ ([変更可能](../50-api-reference/10-configuration.html#package)) を生成します:

- カスタムで `include`/`exclude` オプションを [設定](../50-api-reference/10-configuration.html#package) しない限り、`src/lib` にある全てのファイルが含まれます。Svelte コンポーネントはプリプロセスされ、TypeScript ファイルは JavaScript にトランスパイルされます。
- Svelte、JavaScript、TypeScriptファイルのために生成される型定義 (`d.ts` ファイル)。これには `typescript >= 4.0.0` をインストールする必要があります。型定義は実装の隣に置かれ、手書きの `d.ts` ファイルはそのままコピーされます。[生成を無効化](../50-api-reference/10-configuration.html#package) することもできますが、あまりおすすめしません。
- プロジェクトのルート(root)からコピーされた `package.json` から、`"scripts"`、`"publishConfig.directory"`、`"publishConfig.linkDirectory"` フィールドを取り除いたもの。`"dependencies"` フィールドは含まれているため、ドキュメントやデモサイトにのみ必要なパッケージは `"devDependencies"` に追加してください。`"type": "module"` と `"exports"` フィールドは、オリジナルのファイルで定義されていない場合に追加されます。

`"exports"` フィールドにはパッケージのエントリーポイントが含まれます。デフォルトでは、アンダースコアで始まるファイル(またはアンダースコアで始まるディレクトリにあるファイル)を除いて、`src/lib` にある全てのファイルをエントリーポイントとして扱いますが、この動作は [設定可能](../50-api-reference/10-configuration.html#package) です。もし `src/lib/index.js` や `src/lib/index.svelte` ファイルがある場合は、それがパッケージルートとして扱われます。

例えば、`src/lib/Foo.svelte` コンポーネントと、それを再エクスポートした `src/lib/index.js` モジュールがあった場合、ライブラリの利用者は次のどちらかを行うことができます。

```js
// @filename: ambient.d.ts
declare module 'your-library';

// @filename: index.js
// cut---
import { Foo } from 'your-library';
```

```js
// @filename: ambient.d.ts
declare module 'your-library/Foo.svelte';

// @filename: index.js
// cut---
import Foo from 'your-library/Foo.svelte';
```

> SvelteKit プロジェクトで利用することだけを意図している場合を除いて、`$app` などの [SvelteKit 固有のモジュール](../50-api-reference/30-modules.html) をあなたのパッケージで使用するのは避けてください。例えば、`import { browser } from '$app/environment'` を使用するよりも、[`import.meta.env.SSR`](https://vitejs.dev/guide/env-and-mode.html#env-variables) を使用して全ての Vite ベースのプロジェクトで使用できるようにするか、もっと良いのは [Node conditional exports](https://nodejs.org/api/packages.html#conditional-exports) を使用して全てのバンドラーで動作するようにすることです。また、`$app/stores` や `$app/navigation` などに直接依存せずに、現在の URL やナビゲーションアクション(navigation action)などをプロパティとして渡したいケースもあるでしょう。より一般的な方法でアプリを書くことで、テストや UI デモなどのためのツールのセットアップも簡単になります。

## Options

`svelte-package` は以下のオプションを受け付けます:

- `-w`/`--watch` — `src/lib` の中にあるファイルを監視し、パッケージを再ビルドします

## Publishing

生成されたパッケージをパブリッシュするには:

```sh
npm publish ./package
```

上記の `./package` は生成されるディレクトリ名を参照しています。カスタムで [`package.dir`](../50-api-reference/10-configuration.html#package) を設定している場合は、適宜変更してください。

## 注意事項

相対ファイルのインポートはすべて、Node の ESM アルゴリズムに従って完全に指定する必要があります。つまり、`src/lib/something/index.js` ファイルを `import { something } from './something` のようにインポートすることはできません。代わりに、`import { something } from './something/index.js` というようにインポートする必要があります。TypeScript を使用している場合は、`.ts` ファイルを同じ方法でインポートする必要がありますが、ファイルの末尾は `.ts` ではなく `.js` を使用します (これは我々の管理下ではなく、TypeScript チームが決定したことです)。`tsconfig.json` または `jsconfig.json` で `"moduleResolution": "NodeNext"` と設定することで、この問題を解決できます。

比較的、これは実験的な機能であり、まだ完全に実装されていません。Svelte ファイル(プリプロセス済)と TypeScript ファイル(JavaScriptにトランスパイル済)を除き、全てのファイルはそのままコピーされます。
<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>
