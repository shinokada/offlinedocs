
# Errors


ソフトウェア開発において、エラーは避けられないものです。SvelteKit では、エラーが発生した場所、エラーの種類、受信したリクエストの性質に応じて、異なる方法でエラーを処理します。

## Error objects

SvelteKit は想定されるエラーと予期せぬエラーを区別します。どちらもデフォルトではシンプルな `{ message: string }` オブジェクトとして表現されます。

以下のように、`code` やトラッキング `id` を追加することができます。

## Expected errors

想定されるエラーとは、`@sveltejs/kit` からインポートされる [`error`](../50-api-reference/30-modules.html#sveltejs-kit-error) を使用して作成されるものを指します:

```js
/// file: src/routes/blog/[slug]/+page.server.js
// @filename: ambient.d.ts
declare module '$lib/server/database' {
	export function getPost(slug: string): Promise<{ # string, content: string } | undefined>
}

// @filename: index.js
// cut---
import { error } from '@sveltejs/kit';
import * as db from '$lib/server/database';

/** @type {import('./$types').PageServerLoad} */
export async function load({ params }) {
	const post = await db.getPost(params.slug);

	if (!post) {
		throw error(404, {
			message: 'Not found'
		});
	}

	return { post };
}
```

こうすると、SvelteKit はレスポンスのステータスコードを 404 に設定し、[`+error.svelte`](../20-core-concepts/10-routing.html#error) コンポーネントをレンダリングします。`$page.error` は `error(...)` に第二引数として渡されたオブジェクトです。

```svelte
/// file: src/routes/+error.svelte
<script>
	import { page } from '$app/stores';
</script>

<h1>{$page.error.message}</h1>
```

必要に応じて、エラーオブジェクトにプロパティを追加することができます…

```diff
throw error(404, {
	message: 'Not found',
+	code: 'NOT_FOUND'
});
```

…追加しない場合は、便宜上、文字列を第二引数に渡すことができます:

```diff
-throw error(404, { message: 'Not found' });
+throw error(404, 'Not found');
```

## Unexpected errors

予期せぬエラーとは、リクエストの処理中に発生するその他の例外のことを指します。これらは機密情報を含むことがあるため、予期せぬエラーのメッセージとスタックトレースはユーザーには公開されません。

デフォルトでは、予期せぬエラーはコンソール (または、本番環境では、サーバーログ) に出力され、ユーザーに公開されるエラーはこのように汎用的な形式です。

```json
{ "message": "Internal Error" }
```

予期せぬエラーは [`handleError`](../30-advanced/20-hooks.html#shared-hooks-handleerror) hook を通ります。ここで、独自のエラーハンドリングを追加することができます。例えば、レポーティングサービスにエラーを送ったり、カスタムのエラーオブジェクトを返したりすることができます。

```js
/// file: src/hooks.server.js
// @errors: 2322 2571
// @filename: ambient.d.ts
const Sentry: any;

// @filename: index.js
// cut---
/** @type {import('@sveltejs/kit').HandleServerError} */
export function handleError({ error, event }) {
	// example integration with https://sentry.io/
	Sentry.captureException(error, { event });

	return {
		message: 'Whoops!',
		code: error.code ?? 'UNKNOWN'
	};
}
```

## Responses

もし `handle` の中や [`+server.js`](../20-core-concepts/10-routing.html#server) リクエストハンドラの中でエラーが発生した場合、SvelteKit はリクエストの `Accept` ヘッダー に応じて、フォールバックエラーページか、エラーオブジェクトの JSON 表現をレスポンスとして返します。

`src/error.html` ファイルを追加することで、フォールバックエラーページをカスタマイズすることができます:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="utf-8" />
		<title>%sveltekit.error.message%</title>
	</head>
	<body>
		<h1>My custom error page</h1>
		<p>Status: %sveltekit.status%</p>
		<p>Message: %sveltekit.error.message%</p>
	</body>
</html>
```

SvelteKit が `%sveltekit.status%` と `%sveltekit.error.message%` を、それぞれ対応する値に置き換えます。

ページのレンダリング中に `load` 関数の中でエラーが発生した場合、SvelteKit はエラーが発生した場所に最も近い [`+error.svelte`](../20-core-concepts/10-routing.html#error) コンポーネントをレンダリングします。

例外は、最上位の `+layout.js` や `+layout.server.js` の中でエラーが発生した場合です。通常、最上位のレイアウトには `+error.svelte` コンポーネントが含まれているためです。この場合、SvelteKit はフォールバックエラーページを使用します。

## Type safety

もし TypeScript を使用していてエラーの形式をカスタマイズする必要がある場合、アプリで `App.Error` インターフェイスを宣言することでそれができます (慣習ではこれを `src/app.d.ts` に書きますが、TypeScript が '参照' することができればどこでも構いません):

```ts
/// file: src/app.d.ts
namespace App {
	interface Error {
		code: string;
		id: string;
	}
}
```

このインターフェイスは常に `message: string` プロパティを含んでいます。<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>
