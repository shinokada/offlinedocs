
# Server-only modules


良き友人のように、SvelteKit はあなたの秘密を守ります。バックエンドとフロントエンドが同じリポジトリにある場合、機密データをフロントエンドのコードに誤ってインポートしてしまうことが簡単に起こってしまいます (例えば、API キーを持つ環境変数など)。SvelteKit はこれを完全に防ぐ方法を提供します: サーバー専用のモジュール(server-only modules)です

## Private environment variables

[modules](../50-api-reference/30-modules.html) セクションで説明されている `$env/static/private` モジュールと `$env/dynamic/private` モジュールは、[`hooks.server.js`](../30-advanced/20-hooks.html#server-hooks) や [`+page.server.js`](../20-core-concepts/10-routing.html#page-page-server-js) のようなサーバー上でのみ実行されるモジュールにのみインポートすることが可能です。

## Your modules

モジュールをサーバー専用にするには2通りの方法があります:

- ファイル名に `.server` を付けます。例: `secrets.server.js`
- モジュールを `$lib/server` に置きます。例: `$lib/server/secrets.js`

## How it works

パブリックに公開されるコード (public-facing code) にサーバー専用のコードを (直接的かまたは間接的かにかかわらず) インポートすると…

```js
// @errors: 7005
/// file: $lib/server/secrets.js
export const atlantisCoordinates = [/* redacted */];
```

```js
// @errors: 2307 7006
/// file: src/routes/utils.js
export { atlantisCoordinates } from '$lib/server/secrets.js';

export const add = (a, b) => a + b;
```

```html
/// file: src/routes/+page.svelte
<script>
	import { add } from './utils.js';
</script>
```

…SvelteKit はエラーとなります:

```
Cannot import $lib/server/secrets.js into public-facing code:
- src/routes/+page.svelte
	- src/routes/utils.js
		- $lib/server/secrets.js
```

パブリックに公開されるコード `src/routes/+page.svelte` は、`add` を使用しているのみで、シークレットの `atlantisCoordinates` を使用していませんが、ブラウザがダウンロードする JavaScript にシークレットなコードが残ってしまう可能性があり、このインポートチェーンは安全ではないと考えられます。

この機能は動的なインポート(dynamic imports)でも動作し、``await import(`./${foo}.js`)`` のような補完されたインポートに対しても有効ですが、小さい注意点があります。もしパブリックに公開されるコードとサーバー専用のモジュールの間に2つ以上の dynamic imports がある場合、コードが最初にロードされるときに不正なインポートが検出されない可能性があります。<span style='float: footnote;'><a href="../../index.html#toc">Go to TOC</a></span>