
# Page options


デフォルトでは、SvelteKit はどのコンポーネントも最初はサーバーでレンダリング (または [プリレンダリング](../60-appendix/30-glossary.html#prerendering)) し、それを HTML としてクライアントに送信します。その後、ブラウザ上でコンポーネントを再度レンダリングし、**ハイドレーション(hydration)** と呼ばれるプロセスでそれをインタラクティブなものにします。このため、コンポーネントが両方の場所で実行できることを確認する必要があります。SvelteKit はそれから [**router**](../20-core-concepts/10-routing.html) を初期化し、その後のナビゲーションを引き継ぎます。

これらはそれぞれオプションを [`+page.js`](../20-core-concepts/10-routing.html#page-page-js) や [`+page.server.js`](../20-core-concepts/10-routing.html#page-page-server-js) からエクスポートすることでページごとに、または共有の [`+layout.js`](../20-core-concepts/10-routing.html#layout-layout-js) や [`+layout.server.js`](../20-core-concepts/10-routing.html#layout-layout-server-js) を使用してページグループごとに制御することが可能です。アプリ全体に対してオプションを定義するには、最上位のレイアウト(root layout)からそれをエクスポートします。子レイアウトとページは親レイアウトで設定された値を上書きするため、例えば、プリレンダリングをアプリ全体で有効にし、それから動的にレンダリングする必要があるページではそれを無効にすることができます。

アプリの様々な領域でこれらのオプションをうまく組み合わせることができます。例えば、マーケティングページは高速化を最大限にするためにプリレンダリングし、動的なページは SEO とアクセシビリティのためにサーバーでレンダリングし、管理者用のセクションはクライアントのみでレンダリングするようにして SPA にすることができます。このように、SvelteKit はとても万能で多くの用途にお使いいただけます。

## prerender

あなたのアプリの、少なくともいくつかのルートは、ビルド時に生成されるシンプルな HTML ファイルとして表現されることが多いでしょう。これらのルート(routes)を [_プリレンダリング_](../60-appendix/30-glossary.html#prerendering) することができます。

```js
/// file: +page.js/+page.server.js/+server.js
export const prerender = true;
```

代わりに、`export const prerender = true` を最上位(root)の `+layout.js` または `+layout.server.js` に設定し、明示的にプリレンダリングしないものとしてマークされたページを除き、全てをプリレンダリングできます:

```js
/// file: +page.js/+page.server.js/+server.js
export const prerender = false;
```

`prerender = true` があるルート(routes)は動的な SSR を行うのに使用する manifest から除外されるため、サーバー (または serverless/edge functions) を小さくすることができます。場合によっては、ルート(route)をプリレンダリングしつつ、manifest にも含めたいことがあるでしょう (例えば、`/blog/[slug]` のようなルート(route)があり、最も新しい/人気のあるコンテンツはプリレンダリングしたいがめったにアクセスされないものはサーバーでレンダリングしたい、など)。こういったケースのために、3つ目のオプションがあります、'auto' です:

```js
/// file: +page.js/+page.server.js/+server.js
export const prerender = 'auto';
```

> もしアプリ全体がプリレンダリングに適している場合は、[`adapter-static`](https://github.com/sveltejs/kit/tree/master/packages/adapter-static) を使うことで、任意の静的 Web サーバーで使用するのに適したファイルを出力することができます。

プリレンダラはアプリの最上位(root)から開始され、プリレンダリング可能なページや `+server.js` ルート(routes)を見つけると、そのファイルを生成します。各ページは、プリレンダリングの候補である他のページを指し示す `<a>` 要素を見つけるためにスキャンされます。このため、通常はどのページにアクセスすべきか指定する必要はありません。もしプリレンダラがアクセスするページを指定する必要がある場合は、[prerender configuration](../50-api-reference/10-configuration.html#prerender) の `entries` オプションでこれを指定することができます。

### Prerendering server routes

他のページオプションとは違い、`prerender` は `+server.js` ファイルにも適用できます。これらのファイルはレイアウトから影響を受けませんが、そこからデータを読み込むページからデフォルトの値を継承します。例えば、`+page.js` がこの `load` 関数を含む場合…

```js
/// file: +page.js
export const prerender = true;

/** @type {import('./$types').PageLoad} */
export async function load({ fetch }) {
	const res = await fetch('/my-server-route.json');
	return await res.json();
}
```

…それから `src/routes/my-server-route.json/+server.js` は、自身の `export const prerender = false` を含んでいなければ、プリレンダリング可能であると扱われることになります。

### プリレンダリングしない場合

基本的なルールは次の通りです: ページがプリレンダリング可能であると言うためには、そのページを直接表示する2人のユーザーが、サーバーから同じコンテンツを取得できなけれなりません。

> 全てのページがプリレンダリングに適しているわけではありません。プリレンダリングされたコンテンツは全てのユーザーに表示されます。もちろん、プリレンダリングされたページの `onMount` でパーソナライズされたデータをフェッチできますが、ブランクの初期コンテンツやローディングインジケーターにより、ユーザエクスペリエンスが低下してしまう可能性があります。

`src/routes/blog/[slug]/+page.svelte` ルート(route)のような、ページのパラメータを元にデータをロードするページもプリレンダリングができることにご注意ください。

プリレンダリング中に [`url.searchParams`](../20-core-concepts/20-load.html#using-url-data-url) にアクセスすることは禁止されています。もし使う必要があるなら、ブラウザの中だけで行うようにしてください (例えば `onMount` の中で)。

[action](../20-core-concepts/30-form-actions.html) 付きのページは、サーバーがその action の `POST` リクエストを処理できなければならないため、プリレンダリングできません。

### ルートの衝突(Route conflicts)

プリレンダリングはファイルシステムに書き込むため、ディレクトリとファイルが同じ名前になるエンドポイントを2つ持つことはできません。例えば、`src/routes/foo/+server.js` と `src/routes/foo/bar/+server.js` の場合は、`foo` と `foo/bar` を作成しようとしますが、これは不可能です。

このため(他にも理由はありますが)、常に拡張子を付けておくことを推奨します — `src/routes/foo.json/+server.js` と `src/routes/foo/bar.json/+server.js` は、`foo.json` と `foo/bar.json` ファイルが並んで調和して共存できます。

ページの場合は、`foo` ではなく `foo/index.html` を書き込むことでこの問題を回避しています。

ルーターがすでにアクティブかどうかにかかわらず、このページからのすべてのナビゲーションでクライアントサイドルーティングが無効になることにご注意ください。

### Troubleshooting

'The following routes were marked as prerenderable, but were not prerendered' というようなエラーが表示されたら、それは該当のルート (またはページの場合は親レイアウト) に `export const prerender = true` があるにもかかわらず実際にはそのページがプリレンダリングされていないことが原因です (プリレンダリングクローラーがそのページにアクセスしていないため)。

これらのルート(route)は動的にサーバーレンダリングできないため、該当のルート(route)にアクセスしようとしたときにエラーが発生します。それを解決するには、2つの方法があります:

* SvelteKit が [`config.kit.prerender.entries`](../50-api-reference/10-configuration.html#prerender) からのリンクを辿ってそのルート(route)を見つけられることを確認してください。動的なルート(例えば `[parameters]` を持つページ) へのリンクは、他のエントリーポイントをクローリングしても見つからない場合にこのオプションに追加してください。そうしないと、SvelteKit はその parameters が持つべき値がわからないので、プリレンダリングされません。プリレンダリング可能(prerenderable)なページとしてマークされていないページは無視され、そのページから他のページ(プリレンダリング可能なものも含む)へのリンクもクローリングされません。
* `export const prerender = true` から `export const prerender = 'auto'` に変更してください。`'auto'` になっているルート(route)は動的にサーバーレンダリングすることができます

## ssr

通常、SvelteKit ではページを最初にサーバーでレンダリングし、その HTML をクライアントに送信してハイドレーションを行います。もし `ssr` を `false` に設定した場合、代わりに空の 'shell' ページがレンダリングされます。これはページがサーバーでレンダリングできない場合には便利 (例えば `document` などのブラウザオンリーな globals を使用するなど) ですが、ほとんどの状況では推奨されません ([appendix をご参照ください](../60-appendix/30-glossary.html#ssr))。

```js
/// file: +page.js
export const ssr = false;
```

`export const ssr = false` を最上位(root)の `+layout.js` に追加した場合、アプリ全体がクライアントのみでレンダリングされるようになり、それはつまり、本質的にはアプリを SPA にする、ということを意味します。

## csr

通常、SvelteKit はサーバーでレンダリングされた HTML を、クライアントサイドレンダリング(CSR)されたインタラクティブなページに [ハイドレーション](../60-appendix/30-glossary.html#hydration) します。JavaScript を全く必要としないページもあります。多くのブログ記事や 'about' ページがこのカテゴリに入ります。このような場合は CSR を無効にすることができます:

```js
/// file: +page.js
export const csr = false;
```

> `ssr` to `csr` の両方が `false` である場合は、何もレンダリングされません！

## trailingSlash

デフォルトでは、SvelteKit は URL から末尾のスラッシュ(trailing slash)を取り除きます。`/about/` にアクセスすると、`/about` へのリダイレクトをレスポンスとして受け取ることになります。この動作は、`trailingSlash` オプションで変更することができます。指定できる値は `'never'` (デフォルト)、`'always'`、`'ignore'` です。

他のページオプションと同様に、`+layout.js` や `+layout.server.js` からこの値をエクスポートすると、すべての子のページに適用されます。`+server.js` ファイルからその設定をエクスポートすることもできます。

```js
/// file: src/routes/+layout.js
export const trailingSlash = 'always';
```

このオプションは [プリレンダリング](#prerender) にも影響します。`trailingSlash` が `always` の場合 `/about` というルート(route)は `about/index.html` ファイルとなり、それ以外の場合は `about.html` が作成され、静的な Web サーバの慣習を反映したものになります。

> 末尾のスラッシュを無視することは推奨されません。相対パスのセマンティクスが2つのケースで異なり(`/x` からの `./y` は `/y` ですが、`/x/` からは `/x/y` となります)、`/x` と `/x/` は別の URL として扱われ、SEO 上有害となるからです。
<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>
