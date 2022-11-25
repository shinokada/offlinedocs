
# Loading data


[`+page.svelte`](../20-core-concepts/10-routing.html#page-page-svelte) コンポーネント (と [`+layout.svelte`](../20-core-concepts/10-routing.html#layout-layout-svelte) コンポーネント) をレンダリングする前に、データを取得する必要があるケースが多いでしょう。`load` 関数を定義することでこれができるようになります。

## Page data

`+page.svelte` ファイルは、`load` 関数をエクスポートする `+page.js` (または `+page.ts`) という兄弟ファイルを持つことができ、`load` 関数の戻り値は page で `data` プロパティを介して使用することができます。

```js
/// file: src/routes/blog/[slug]/+page.js
/** @type {import('./$types').PageLoad} */
export function load({ params }) {
	return {
		post: {
			# `Title for ${params.slug} goes here`,
			content: `Content for ${params.slug} goes here`
		}
	};
}
```

```svelte
/// file: src/routes/blog/[slug]/+page.svelte
<script>
	/** @type {import('./$types').PageData} */
	export let data;
</script>

<h1>{data.post.title}</h1>
<div>{@html data.post.content}</div>
```

生成される `$types` モジュールのおかげで、完全な型安全性を確保できます。

`+page.js` ファイルの `load` 関数はサーバーでもブラウザでも実行されます。`load` 関数を _常に_ サーバーで実行させたければ (例えばプライベートな環境変数を使用していたり、データベースにアクセスする場合など)、代わりに `+page.server.js` に `load` 関数を置くとよいでしょう。

例えばブログ記事の `load` 関数をより現実的なものにするとしたら、以下のように、サーバー上でのみ実行され、データベースからデータを取得する、というものになるでしょう。

```js
/// file: src/routes/blog/[slug]/+page.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getPost(slug: string): Promise<{ # string, content: string }>
}

// @filename: index.js
// cut---
import * as db from '$lib/server/database';

/** @type {import('./$types').PageServerLoad} */
export async function load({ params }) {
	return {
		post: await db.getPost(params.slug)
	};
}
```

型が `PageLoad` から `PageServerLoad` に変わっていることにご注意ください。サーバー専用の `load` 関数では追加の引数にアクセスすることができます。どのような場合に `+page.js` を使用し、どのような場合に `+page.server.js` を使用するのかを理解するには、[Shared vs server](../20-core-concepts/20-load.html#shared-vs-server) を参照してください。

## Layout data

`+layout.svelte` ファイルでも、`+layout.js` や `+layout.server.js` を通じてデータを読み込むことができます。

```js
/// file: src/routes/blog/[slug]/+layout.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getPostSummaries(): Promise<Array<{ # string, slug: string }>>
}

// @filename: index.js
// cut---
import * as db from '$lib/server/database';

/** @type {import('./$types').LayoutServerLoad} */
export async function load() {
	return {
		posts: await db.getPostSummaries()
	};
}
```

```svelte
/// file: src/routes/blog/[slug]/+layout.svelte
<script>
	/** @type {import('./$types').LayoutData} */
	export let data;
</script>

<main>
	<!-- +page.svelte is rendered here -->
	<slot />
</main>

<aside>
	<h2>More posts</h2>
	<ul>
		{#each data.posts as post}
			<li>
				<a href="/blog/{post.slug}">
					{post.title}
				</a>
			</li>
		{/each}
	</ul>
</aside>
```

レイアウトの `load` 関数から返されたデータは、子の `+layout.svelte` コンポーネントやそのレイアウトに属する `+page.svelte` コンポーネントでも利用可能です。

```diff
/// file: src/routes/blog/[slug]/+page.svelte
<script>
+	import { page } from '$app/stores';

	/** @type {import('./$types').PageData} */
	export let data;

+	// we can access `data.posts` because it's returned from
+	// the parent layout `load` function
+	$: index = data.posts.findIndex(post => post.slug === $page.params.slug);
+	$: next = data.posts[index - 1];
</script>

<h1>{data.post.title}</h1>
<div>{@html data.post.content}</div>

+{#if next}
+	<p>Next post: <a href="/blog/{next.slug}">{next.title}</a></p>
+{/if}
```

> 複数の `load` 関数が同じキーを持つデータを返した場合、最後に返したものが'勝ちます'。レイアウトの `load` が `{ a: 1, b: 2 }` を返し、ページの `load` が `{ b: 3, c: 4 }` を返すと、結果は `{ a: 1, b: 3, c: 4 }` となります。

## $page.data

`+page.svelte` コンポーネントとその上の各 `+layout.svelte` コンポーネントは、自身のデータとその親の全てのデータにアクセスすることができます。

場合によっては、その逆も必要かもしれません — 親レイアウトからページのデータや子レイアウトのデータにアクセスする必要があるかもしれません。例えば、最上位のレイアウト(root layout)から、`+page.js` や `+page.server.js` の `load` 関数から返された `title` プロパティにアクセスしたい場合があるでしょう。これは `$page.data` で行うことができます:

```svelte
/// file: src/routes/+layout.svelte
<script>
	import { page } from '$app/stores';
</script>

<svelte:head>
	<title>{$page.data.title}</title>
</svelte:head>
```

`$page.data` の型情報は `App.PageData` から提供されます。

## Shared vs server

これまで見てきたように、`load` 関数には2つの種類があります:

* `+page.js` ファイルと `+layout.js` ファイルがエクスポートする `load` 関数は、サーバーとブラウザで共有(shared)されます
* `+page.server.js` ファイルと `+layout.server.js` ファイルがエクスポートする `load` 関数は、サーバー専用です

概念上は同じものですが、気をつけなければならない重要な違いがあります。

### Input

共有される `load` 関数とサーバー専用の `load` 関数はどちらも、リクエストを表すプロパティ (`params`、`route`、`url`) と様々な関数 (`depends`、`fetch`、`parent`) にアクセスできます。これらについては、以下のセクションで説明します。

サーバー専用の `load` 関数は `ServerLoadEvent` を引数にとって呼び出されます。`ServerLoadEvent` は、`RequestEvent` から `clientAddress`、`cookies`、`locals`、`platform`、`request` を継承しています。

共有される `load` 関数は、`LoadEvent` を引数にとって呼び出されます。`LoadEvent` は `data` プロパティを持っています。もし `+page.js` と `+page.server.js` (または `+layout.js` と `+layout.server.js`) の両方に `load` 関数がある場合、サーバー専用の `load` 関数の戻り値が、共有される `load` 関数の引数の `data` プロパティとなります。

### Output

共有される `load` 関数は、任意の値(カスタムクラスやコンポーネントコンストラクタなどを含む)を含むオブジェクトを返すことができます。

サーバー専用の `load` 関数は、ネットワークで転送できるようにするために、[devalue](https://github.com/rich-harris/devalue) でシリアライズできるデータ、つまり JSON で表現できるものに加え、`BigInt`、`Date`、`Map`、`Set`、`RegExp` や、繰り返し/循環参照などを返さなければなりません。

### どちらを使用すべきか

サーバー専用の `load` 関数は、データベースやファイルシステムからデータを直接アクセスする必要がある場合や、プライベートな環境変数を使用する必要がある場合に有用です。

共有される `load` 関数は、外部の API から データを `fetch` (取得) する必要があり、プライベートなクレデンシャルが必要ない場合に便利です。なぜなら、SvelteKit はあなたのサーバーを経由せずに、その API から直接データを取得することができるからです。また、Svelte コンポーネントコンストラクタのような、シリアライズできないものを返す必要がある場合にも便利です。

まれに、両方を同時に使用する必要がある場合もあります。例えば、サーバーからのデータで初期化されたカスタムクラスのインスタンスを返す必要がある場合です。

## URL data を使用する

多くの場合、`load` 関数は何らかの形で URL に依存します。そのため、`load` 関数では `url`、`route`、`params` を提供しています。

### url

[`URL`](https://developer.mozilla.org/ja/docs/Web/API/URL) のインスタンスで、`origin`、`hostname`、`pathname`、`searchParams` ([`URLSearchParams`](https://developer.mozilla.org/ja/docs/Web/API/URLSearchParams) オブジェクトとしてパースされたクエリ文字列を含む) を含んでいます。`url.hash` はサーバーで利用できないため、`load` 中にアクセスすることはできません。

> 環境によっては、サーバーサイドレンダリング時のリクエストヘッダからこれが導出されることもあります。例えば [adapter-node](../20-core-concepts/50-adapters.html#supported-environments-node-js) では、URL を正しく設定するために adapter の設定をする必要があるかもしれません。

### route

現在のルート(route)ディレクトリの名前を含んでいます。`src/routes` との相対です:

```js
/// file: src/routes/a/[b]/[...c]/+page.js
/** @type {import('./$types').PageLoad} */
export function load({ route }) {
	console.log(route.id); // '/a/[b]/[...c]'
}
```

### params

`params` は `url.pathname` と `route.id` から導出されます。

`route.id` が `/a/[b]/[...c]` で、`url.pathname` が `/a/x/y/z` の場合、`params` オブジェクトはこのようになります:

```json
{
	"b": "x",
	"c": "y/z"
}
```

## fetch リクエストの作成

外部の API や `+server.js` ハンドラからデータを取得するには、提供されている `fetch` 関数を使用します。これは [ネイティブの `fetch` web API](https://developer.mozilla.org/ja/docs/Web/API/fetch) と同じように動作しますが、いくつか追加の機能があります:

- ページリクエストの `cookie` と `authorization` ヘッダーを継承するので、サーバー上でクレデンシャル付きのリクエストを行うことができます
- サーバー上で、相対パスのリクエストを行うことができます (通常、`fetch` はサーバーのコンテキストで使用する場合にはオリジン付きの URL が必要です)
- サーバーで動作している場合、内部リクエスト (例えば `+server.js` ルート(routes)に対するリクエスト) は直接ハンドラ関数を呼び出すので、HTTP を呼び出すオーバーヘッドがありません
- サーバーサイドレンダリング中は、レスポンスはキャプチャされ、レンダリング済の HTML にインライン化されます。ヘッダーは、[`filterSerializedResponseHeaders`](../30-advanced/20-hooks.html#server-hooks-handle) で明示的に指定されない限り、シリアライズされないことにご注意ください。そして、ハイドレーション中は、レスポンスは HTML から読み込まれるため、一貫性が保証され、追加のネットワークリクエストを防ぎます。もし、`load` 関数の `fetch` ではなくブラウザの `fetch` を使用しているときにブラウザコンソールに警告が出た場合は、これが理由です。

```js
/// file: src/routes/items/[id]/+page.js
/** @type {import('./$types').PageLoad} */
export async function load({ fetch, params }) {
	const res = await fetch(`/api/items/${params.id}`);
	const item = await res.json();

	return { item };
}
```

> Cookie は、ターゲットホストが Sveltekit アプリケーションと同じか、より明確・詳細(specific)なサブドメインである場合にのみ引き渡されます。

## Cookies and headers

サーバー専用の `load` 関数では [`cookies`](../50-api-reference/40-types.html#sveltejs-kit-cookies) を取得したり設定したりすることができます。

```js
/// file: src/routes/+layout.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getUser(sessionid: string | undefined): Promise<{ name: string, avatar: string }>
}

// @filename: index.js
// cut---
import * as db from '$lib/server/database';

/** @type {import('./$types').LayoutServerLoad} */
export async function load({ cookies }) {
	const sessionid = cookies.get('sessionid');

	return {
		user: await db.getUser(sessionid)
	};
}
```

> cookie を設定するときは、`path` プロパティにご注意ください。デフォルトでは、cookie の `path` は現在のパス名です。例えば、`admin/user` ページで cookie を設定した場合、デフォルトではその cookie は `admin` ページ配下でのみ使用することができます。多くの場合、`path` を `'/'` に設定して、アプリ全体で cookie を使用できるようにしたいでしょう。

サーバー専用の `load` 関数と共有される `load` 関数はどちらも `setHeaders` 関数にアクセスでき、サーバー上で実行している場合、 レスポンスにヘッダーを設定できます。(ブラウザで実行している場合、`setHeaders` には何の効果もありません。) これは、ページをキャッシュさせる場合に便利です、例えば:

```js
// @errors: 2322
/// file: src/routes/products/+page.js
/** @type {import('./$types').PageLoad} */
export async function load({ fetch, setHeaders }) {
	const url = `https://cms.example.com/products.json`;
	const response = await fetch(url);

	// cache the page for the same length of time
	// as the underlying data
	setHeaders({
		age: response.headers.get('age'),
		'cache-control': response.headers.get('cache-control')
	});

	return response.json();
}
```

同じヘッダーを複数回設定することは (たとえ別々の `load` 関数であっても) エラーとなります。指定したヘッダーを設定できるのは一度だけです。`set-cookie` ヘッダーは、`setHeaders` では追加できません。代わりに、`cookies.set(name, value, options)` を使用してください。

## Using parent data

`load` 関数が親の `load` 関数からのデータにアクセスできたら便利なことがあるでしょう。`await parent()` でこれを行うことができます:

```js
/// file: src/routes/+layout.js
/** @type {import('./$types').LayoutLoad} */
export function load() {
	return { a: 1 };
}
```

```js
/// file: src/routes/abc/+layout.js
/** @type {import('./$types').LayoutLoad} */
export async function load({ parent }) {
	const { a } = await parent();
	return { b: a + 1 };
}
```

```js
/// file: src/routes/abc/+page.js
/** @type {import('./$types').PageLoad} */
export async function load({ parent }) {
	const { a, b } = await parent();
	return { c: a + b };
}
```

```svelte
<script>
	/** @type {import('./$types').PageData} */
	export let data;
</script>

<!-- renders `1 + 2 = 3` -->
<p>{data.a} + {data.b} = {data.c}</p>
```

> `+page.js` の `load` 関数が、直接の親だけなく、両方のレイアウトの `load` 関数からマージされたたデータを受け取っていることにご注意ください。

`+page.server.js` と `+layout.server.js` の中では、`parent` は親の `+layout.server.js` ファイルからデータを取得します。

`+page.js` や `+layout.js` では、親の `+layout.js` ファイルからデータを返します。しかし、`+layout.js` が見つからない場合は `({ data }) => data` 関数として扱われます。つまり、`+layout.js` ファイルによって'シャドウ(shadowed)'されていない親の `+layout.server.js` ファイルからデータを返します。 

`await parent()` を使用する際はウォータフォールを発生させないよう注意してください。例えば、こちらの `getData(params)` は `parent()` の呼び出しの結果には依存しないので、レンダリングの遅延を避けるために最初に呼び出すほうが良いでしょう。

```difこ
/// file: +page.js
/** @type {import('./$types').PageLoad} */
export async function load({ params, parent }) {
-	const parentData = await parent();
	const data = await getData(params);
+	const parentData = await parent();

	return {
		...data
		meta: { ...parentData.meta, ...data.meta }
	};
}
```

## Errors

`load` 中にエラーがスローされた場合、最も近くにある [`+error.svelte`](../20-core-concepts/10-routing.html#error) がレンダリングされます。想定されるエラーには、`@sveltejs/kit` からインポートできる `error` ヘルパーを使用して HTTP ステータスコードとオプションのメッセージを指定できます:

```js
/// file: src/routes/admin/+layout.server.js
// @filename: ambient.d.ts
declare namespace App {
	interface Locals {
		user?: {
			name: string;
			isAdmin: boolean;
		}
	}
}

// @filename: index.js
// cut---
import { error } from '@sveltejs/kit';

/** @type {import('./$types').LayoutServerLoad} */
export function load({ locals }) {
	if (!locals.user) {
		throw error(401, 'not logged in');
	}

	if (!locals.user.isAdmin) {
		throw error(403, 'not an admin');
	}
}
```

予期せぬエラーがスローされた場合、SvelteKit は [`handleError`](../30-advanced/20-hooks.html#shared-hooks-handleerror) を呼び出し、それを 500 Internal Error として処理します。

## Redirects

ユーザーをリダイレクトさせるには、`@sveltejs/kit` からインポートできる `redirect` ヘルパーを使用して、ステータスコード `3xx` と一緒にリダイレクト先の location を指定します。

```js
/// file: src/routes/user/+layout.server.js
// @filename: ambient.d.ts
declare namespace App {
	interface Locals {
		user?: {
			name: string;
		}
	}
}

// @filename: index.js
// cut---
import { redirect } from '@sveltejs/kit';

/** @type {import('./$types').LayoutServerLoad} */
export function load({ locals }) {
	if (!locals.user) {
		throw redirect(307, '/login');
	}
}
```

## Promise unwrapping

トップレベルの promise は await されるので、ウォータフォールを作ることなく、複数の promise を簡単に返すことができます:

```js
/// file: src/routes/+page.server.js
/** @type {import('./$types').PageServerLoad} */
export function load() {
	return {
		a: Promise.resolve('a'),
		b: Promise.resolve('b'),
		c: {
			value: Promise.resolve('c')
		}
	};
}
```

```svelte
<script>
	/** @type {import('./$types').PageData} */
	export let data;

	console.log(data.a); // 'a'
	console.log(data.b); // 'b'
	console.log(data.c.value); // `Promise {...}`
</script>
```

## Parallel loading

ページをレンダリング (またはページにナビゲーション) するとき、SvelteKit はすべての `load` 関数を同時に実行し、リクエストのウォーターフォールを回避します。クライアントサイドナビゲーションのときは、複数のサーバー専用 `load` 関数の呼び出し結果が単一のレスポンスにグループ化されます。すべての `load` 関数が返されると、ページがレンダリングされます。

## Invalidation

SvelteKit は それぞれの `load` 関数の依存関係を追跡し、ナビゲーションの際に不必要に再実行されるのを回避します。

例えば、このような `load` 関数のペアがあるとして…

```js
/// file: src/routes/blog/[slug]/+page.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getPost(slug: string): Promise<{ # string, content: string }>
}

// @filename: index.js
// cut---
import * as db from '$lib/server/database';

/** @type {import('./$types').PageServerLoad} */
export async function load({ params }) {
	return {
		post: await db.getPost(params.slug)
	};
}
```

```js
/// file: src/routes/blog/[slug]/+layout.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getPostSummaries(): Promise<Array<{ # string, slug: string }>>
}

// @filename: index.js
// cut---
import * as db from '$lib/server/database';

/** @type {import('./$types').LayoutServerLoad} */
export async function load() {
	return {
		posts: await db.getPostSummaries()
	};
}
```

…もし、`/blog/trying-the-raw-meat-diet` から `/blog/i-regret-my-choices` に移動したら、`params.slug` が変更されているので、`+page.server.js` のほうは再実行されるでしょう。`+layout.server.js` のほうは再実行されません、なぜならデータがまだ有効だからです。言い換えると、`db.getPostSummaries()` を2回呼び出すことはないということです。

`await parent()` を呼び出している `load` 関数は、親の `load` 関数が再実行された場合に再実行されます。

### Manual invalidation

現在のページに適用される `load` 関数は、[`invalidate(url)`](../50-api-reference/30-modules.html#$app-navigation-invalidate) を使用することで再実行させることもできます。これは `url` に依存しているすべての `load` 関数を再実行させるものです。[`invalidateAll()`](../50-api-reference/30-modules.html#$app-navigation-invalidateall) は、すべての `load` 関数を再実行させます。

`load` 関数が `fetch(url)` や `depends(url)` を呼び出している場合、その `load` 関数は `url` に依存しています。`url` には `[a-z]:` から始まるカスタムの識別子を指定することができることにご注意ください:

```js
/// file: src/routes/random-number/+page.js
/** @type {import('./$types').PageLoad} */
export async function load({ fetch, depends }) {
	// load reruns when `invalidate('https://api.example.com/random-number')` is called...
	const response = await fetch('https://api.example.com/random-number');

	// ...or when `invalidate('app:random')` is called
	depends('app:random');

	return {
		number: await response.json()
	};
}
```

```svelte
/// file: src/routes/random-number/+page.svelte
<script>
	import { invalidateAll } from '$app/navigation';

	/** @type {import('./$types').PageData} */
	export let data;

	function rerunLoadFunction() {
		// any of these will cause the `load` function to re-run
		invalidate('app:random');
		invalidate('https://api.example.com/random-number');
		invalidate(url => url.href.includes('random-number'));
		invalidateAll();
	}
</script>

<p>random number: {data.number}</p>
<button on:click={rerunLoadFunction}>Update random number</button>
```

まとめると、`load` 関数は以下のシチュエーションで再実行されます:

- `params` のプロパティを参照していて、その値が変更された場合
- `url` のプロパティを参照していて (例えば `url.pathname` や `url.search`)、その値が変更された場合
- `await parent()` を呼び出していて、親の `load` 関数が再実行されたとき
- [`fetch`](#making-fetch-requests) や [`depends`](../50-api-reference/40-types.html#sveltejs-kit-loadevent) を介して特定の URL に対する依存を宣言していて、その URL が [`invalidate(url)`](../50-api-reference/30-modules.html#$app-navigation-invalidate) で無効(invalid)であるとマークされた場合
- [`invalidateAll()`](../50-api-reference/30-modules.html#$app-navigation-invalidateall) によって全ての有効な `load` 関数が強制的に再実行された場合

`load` 関数の再実行は、対応する `+layout.svelte` や `+page.svelte` 内の `data` プロパティが更新されるだけで、コンポーネントは再作成されることはありません。結果として、内部の状態は保持されます。もし、この挙動がお望みでなければ、[`afterNavigate`](../50-api-reference/30-modules.html#$app-navigation-afternavigate) コールバック内でリセットしたり、コンポーネントを [`{#key ...}`](https://svelte.jp/docs#template-syntax-key) ブロックでラップしたりしてリセットすることができます。

## 状態の共有(Shared state)

多くのサーバー環境では、アプリの単一のインスタンスが複数のユーザーにサービスを提供することになります。そのため、リクエストごと(per-request)、ユーザーごと(per-user)の状態を `load` 関数の外側の共有変数に保存してはいけません。代わりに、`event.locals` に保存するようにしてください。
<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>