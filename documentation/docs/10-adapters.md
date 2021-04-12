---
title: Adapters
---

Before you can deploy your SvelteKit app, you need to _adapt_ it for your deployment target. Adapters are small plugins that take the built app as input and generate output that is optimised for a specific platform.
SvelteKitアプリをデプロイ出来るようになるまでに、デプロイの対象向けに*アダプト*する必要があります。アダプターはビルドされたアプリを入力に取り、特定のプラットフォーム向けに最適化された出力を生成します。

For example, if you want to run your app as a simple Node server, you would use the `@sveltejs/adapter-node` package:
例えば、アプリを単純なNodeサーバーとして実行したい場合、`@sveltejs/adapter-node`パッケージを使うことになるでしょう。

```js
// svelte.config.cjs
const node = require('@sveltejs/adapter-node');

module.exports = {
	kit: {
		adapter: node()
	}
};
```

With this, [svelte-kit build](#command-line-interface-svelte-kit-build) will generate a self-contained Node app inside `build`. You can pass options to adapters, such as customising the output directory in `adapter-node`:
これで[svelte-kit build](#command-line-interface-svelte-kit-build)は`build`の中にxxself-containedxxNodeアプリを生成するようになります。アダプターにオプションを渡すことができます。

```diff
// svelte.config.cjs
const node = require('@sveltejs/adapter-node');

module.exports = {
	kit: {
-		adapter: node()
+		adapter: node({ out: 'my-output-directory' })
	}
};
```

A variety of official adapters exist for serverless platforms...
様々な公式アダプターが存在しています。サーバーレスプラットフォーム向けには…

- [`adapter-begin`](https://github.com/sveltejs/kit/tree/master/packages/adapter-begin) — [begin.com](https://begin.com)向け
- [`adapter-netlify`](https://github.com/sveltejs/kit/tree/master/packages/adapter-netlify) — [netlify.com](https://netlify.com)向け
- [`adapter-vercel`](https://github.com/sveltejs/kit/tree/master/packages/adapter-vercel) — [vercel.com](https://vercel.com)向け

その他のプラットフォーム向けには…

- [`adapter-node`](https://github.com/sveltejs/kit/tree/master/packages/adapter-node) — for creating self-contained Node apps
- [`adapter-static`](https://github.com/sveltejs/kit/tree/master/packages/adapter-static) — for prerendering your entire site as a collection of static files

> The adapter API is still in flux and will likely change before 1.0.
> アダプターAPIはまだ流動的で、1.0の前に変更される可能性が高いです。
