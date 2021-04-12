---
title: Configuration
---

Your project's configuration lives in a `svelte.config.cjs` file. All values are optional. The complete list of options, with defaults, is shown here:
プロジェクトの設定は`svelte.config.cjs`ファイルの中にあります。全ての値が省略可能です。項目の完全なリストをデフォルト値と併せてここに示します。

```js
/** @type {import('@sveltejs/kit').Config} */
module.exports = {
	// options passed to svelte.compile (https://svelte.dev/docs#svelte_compile)
	compilerOptions: null,

	// an array of file extensions that should be treated as Svelte components
	extensions: ['.svelte'],

	kit: {
		adapter: null,
		amp: false,
		appDir: '_app',
		files: {
			assets: 'static',
			hooks: 'src/hooks',
			lib: 'src/lib',
			routes: 'src/routes',
			serviceWorker: 'src/service-worker',
			template: 'src/app.html'
		},
		host: null,
		hostHeader: null,
		hydrate: true,
		paths: {
			assets: '',
			base: ''
		},
		prerender: {
			crawl: true,
			enabled: true,
			force: false,
			pages: ['*']
		},
		router: true,
		ssr: true,
		target: null,
		vite: () => ({})
	},

	// options passed to svelte.preprocess (https://svelte.dev/docs#svelte_preprocess)
	preprocess: null
};
```

### adapter

Determines how the output of `svelte-kit build` is converted for different platforms. See [Adapters](#adapters).
`svelte-kit build`の出力が様々なプラットフォーム向けにどう変換されるかを決めます。[Adapters](#adapters)を参照。

### amp

Enable [AMP](#amp) mode.
[AMP](#amp)モードを有効にします。

### appDir

The directory relative to `paths.assets` where the built JS and CSS (and imported assets) are served from. (The filenames therein contain content-based hashes, meaning they can be cached indefinitely).
`paths.assets`からの相対ディレクトリ
ビルドされたJSとCSS（及びインポートされたアセット）が提供される
（）

### files

An object containing zero or more of the following `string` values:
以下の`string`の値を0個以上含むオブジェクト。

- `assets` — a place to put static files that should have stable URLs and undergo no processing, such as `favicon.ico` or `manifest.json`
- `assets` — 固定のURLを持ち何の処理もされない静的なファイル（`favicon.ico`や`manifest.json`など）をおく場所。
- `lib` — your app's internal library, accessible throughout the codebase as `$lib`
- `lib` — アプリの内部ライブラリ。コードベース全体から`$lib`として利用可能。
- `routes` — the files that define the structure of your app (see [Routing](#routing))
- `routes` — アプリの構造を定義するファイル（[Routing](#routing)を参照）
- `serviceWorker` — the location of your service worker's entry point (see [Service workers](#service-workers))
- `serviceWorker` — service workerのエントリポイントの場所（[Service workers](#service-workers)を参照）
- `hooks` — the location of your hooks module (see [Hooks](#hooks))
- `hooks` — hooksモジュールの場所（[Hooks](#hooks)を参照）
- `template` — the location of the template for HTML responses
- `template` — HTMLレスポンスのテンプレートの場所

### host

A value that overrides the `Host` header when populating `page.host`
xxxx`Host`ヘッダーを上書きする値

### hostHeader

If your app is behind a reverse proxy (think load balancers and CDNs) then the `Host` header will be incorrect. In most cases, the underlying host is exposed via the [`X-Forwarded-Host`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Host) header and you should specify this in your config if you need to access `page.host`:
もし、アプリがリバースプロキシ（ロードバランサーやCDNxxthinkxx）の背後にいると、`Host`が不正確になってしまいます。多くの場合、背後にあるホストは[`X-Forwarded-Host`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Host)ヘッダー経由で公開されており、`page.host`を使う必要があるなら、これを設定で指定しなければいけません。

```js
// svelte.config.cjs
module.exports = {
	kit: {
		hostHeader: 'X-Forwarded-Host'
	}
};
```

**You should only do this if you trust the reverse proxy**, which is why it isn't the default.
**リバースプロキシが信頼できる場合にのみこれをすべきです。**だからこそデフォルトではないのです。

### hydrate

Whether to [hydrate](#ssr-and-javascript-hydrate) the server-rendered HTML with a client-side app. (It's rare that you would set this to `false` on an app-wide basis.)
サーバーで描画されたHTMLをクライアント側のアプリに[hydrate](#ssr-and-javascript-hydrate)するべきか。（アプリ全体としてこれを`false`にすることになるのはまれです。）

### paths

An object containing zero or more of the following `string` values:
以下の`string`の値を0個以上含むオブジェクト。

- `assets` — an absolute path, or a path relative to `base`, where your app's files are served from. This is useful if your files are served from a storage bucket of some kind
- `assets` — アプリのファイルがどこから提供されるかを表す絶対パスまたは`base`からの相対パス。ファイルば何らかのストレージバケットから提供される場合にこれが役に立ちます。
- `base` — a root-relative (i.e. starts with `/`) path that specifies where your app is served from. This allows the app to live on a non-root path
- `base` — アプリがどこから提供されるかを指定するルートからの相対パス（つまり`/`から始まります）。アプリがルート以外のパスに存在できるようになります。

### prerender

See [Prerendering](#ssr-and-javascript-prerender). An object containing zero or more of the following:
[Prerendering](#ssr-and-javascript-prerender)を参照。以下の0個以上を含むオブジェクト。

- `crawl` — determines whether SvelteKit should find pages to prerender by following links from the seed page(s)
- `crawl` — SvelteKitが、xxseedxxページからリンクを辿ることでprerenderの対象ページを探すべきかが決まります
- `enabled` — set to `false` to disable prerendering altogether
- `enabled` — `false`にするとprerender機能を完全に無効にします
- `force` — if `true`, a page that fails to render will _not_ cause the entire build to fail
- `force` — `true`であれば、あるページが描画に失敗してもビルド全体は失敗しなくなります
- `pages` — an array of pages to prerender, or start crawling from (if `crawl: true`). The `*` string includes all non-dynamic routes (i.e. pages with no `[parameters]` )
- `pages` — prerender対象ページまたは（`crawl: true`の場合の）クロールを始めるページの配列。文字列`*`は全ての動的でないルート（つまり`[parameters]`がないページ）を含みます

### router

Enables or disables the client-side [router](#ssr-and-javascript-router) app-wide.
クライアントサイドの[router](#ssr-and-javascript-router)をアプリ全体で有効または無効にします。

### ssr

Enables or disables [server-side rendering](#ssr-and-javascript-ssr) app-wide.
[server-side rendering](#ssr-and-javascript-ssr)をアプリ全体で有効または無効にします。

### target

Specifies an element to mount the app to. It must be a DOM selector that identifies an element that exists in your template file. If unspecified, the app will be mounted to `document.body`.
アプリをマウントする要素を指定します。テンプレートファイルに存在する1つの要素を特定できるDOMセレクター出なければいけません。指定されていない場合、アプリは`document.body`にマウントされることになります。

### vite

A [Vite config object](https://vitejs.dev/config), or a function that returns one. Not all configuration options can be set, since SvelteKit depends on certain values being configured internally.
[Vite設定オブジェクト](https://vitejs.dev/config)またはそれを返す関数。SvelteKitが内部で設定した特定の値に依存するので、全ての設定項目が指定できるわけではありません。
