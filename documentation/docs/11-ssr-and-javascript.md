---
title: SSR and JavaScript
---

By default, SvelteKit will server-render pages on demand, then **hydrate** the rendered HTML in the client with an interactive Svelte app while initialising a **router** that takes over subsequent navigations.
デフォルトでは、SvelteKitは、オンデマンドでページをxxserver-renderxxし、その後クライアントの中で、後続の遷移を引き継ぐ**ルーター**の初期化中に、描画されたHTMLをインタラクティブなSvelteアプリに**ハイドレート**(hydrate)します。

> 訳註: hydrateの原義は、ものに水を吸い込ませることです。

You can control each of these on a per-app or per-page basis. Note that each of the per-page settings use [`context="module"`](https://svelte.dev/docs#script_context_module), and only apply to page components, _not_ [layout](#layouts) components.
アプリ単位またはページ単位にこれらを制御できます。ページ単位の各設定は[`context="module"`](https://svelte.dev/docs#script_context_module)を利用し、ページコンポーネントだけに適用され、[レイアウト](#layouts)コンポーネントには適用され*ない*ことに注意してください。

If both are specified, per-page settings override per-app settings in case of conflicts. Each setting can be controlled independently, but `ssr` and `hydrate` cannot both be `false` since that would result in nothing being rendered at all.
もし両方が指定されていてコンフリクトしている場合、ページ単位の設定がアプリ単位の設定を上書きします。それらの設定は独立に制御できますが、`ssr`と`hydrate`の両方を`false`にすることは、何も描画しないことになってしまうので、できません。

### ssr

Disabling server-side rendering effectively turns your SvelteKit app into a **single-page app** or SPA.
サーバーサイドレンダリングを無効にすると、実質的にSvelteKitアプリは**single-page app**（SPA）になります。

> In most situations this is not recommended: it harms SEO, tends to slow down perceived performance, and makes your app inaccessible to users if JavaScript fails or is disabled (which happens [more often than you probably think](https://kryogenix.org/code/browser/everyonehasjs.html)). Sometimes it's appropriate or even necessary, but consider alternatives before disabling SSR.
> ほとんどの状況でこれは推奨されません。SEOに悪影響を与え、体感速度が遅くなりがちにし、JavaScriptが失敗したり無効になっていたりすると（これは[あなたが思っているよりよくある](https://kryogenix.org/code/browser/everyonehasjs.html)ことです）アプリが利用できなくなります。SSR無効化が適切だったり必要だったりするときもありますが、SSRを無効化する前に代わりの方法を考えてください。

You can disable SSR app-wide with the [`ssr` config option](#configuration-ssr), or a page-level `ssr` export:
[設定オプションの`ssr`](#configuration-ssr)またはページレベルの`ssr`のエクスポートで、SSRをアプリ全体に渡って無効にできます。

```html
<script context="module">
	export const ssr = false;
</script>
```

### router

SvelteKit includes a client-side router that intercepts navigations (from the user clicking on links, or interacting with the back/forward buttons) and updates the page contents, rather than letting the browser handle the navigation by reloading.
SvelteKitは、クライアントサイドのルーターを含んでいます。
ユーザーのリンクをクリックや進む/戻るボタンの操作から
ルーターは遷移を捕捉して、ブラウザにリロードによる遷移をさせるのではなく、ページの内容を更新します。

In certain circumstances you might need to disable this behaviour with the app-wide [`router` config option](#configuration-router) or the page-level `router` export:
ある状況下では、[設定オプションの`router`](#configuration-router)またはページレベルの`router`のエクスポートで、この挙動を無効にする必要があるかもしれません。

```html
<script context="module">
	export const router = false;
</script>
```

Note that this will disable client-side routing for any navigation from this page, regardless of whether the router is already active.
この設定によって、ルーターが既にアクティブかどうかによらず、このページからの遷移に対するクライアンドサイドのルーティングが無効になることに注意してください。


### hydrate

Ordinarily, SvelteKit 'hydrates' your server-rendered HTML into an interactive page. Some pages don't require JavaScript at all — many blog posts and 'about' pages fall into this category. In these cases you can skip hydration when the app boots up with the app-wide [`hydrate` config option](#configuration-hydrate) or the page-level `hydrate` export:
通常、SvelteKitはサーバーで描画されるHTMLをインタラクティブなページにハイドレートします。全くJavaScriptを必要としないページもあります。多くのブログポストやaboutページがこのカテゴリーに入ります。このような場合、[設定オプションの`hydrate`](#configuration-hydrate)またはページレベルの`hydrate`のエクスポートで、アプリ起動時のハイドレーションを飛ばすことができます。

```html
<script context="module">
	export const hydrate = false;
</script>
```

> If `hydrate` and `router` are both `false`, SvelteKit will not add any JavaScript to the page at all.
> `hydrate`と`router`の両方が`false`の場合、SvelteKitはページにJavaScriptを全く追加しなくなります。

### prerender

It's likely that at least some pages of your app can be represented as a simple HTML file, since they contain no dynamic or user-specific data. These pages can be _prerendered_ by your [adapter](#adapters).
アプリのいくつのページが、動的なデータもユーザー固有のデータも含まないので、単なるHTMLファイルで表現されることもよくあります。これらのページは[adapter](#adapters)によって*prerender*されることがあります。

If your entire app is suitable for prerendering, you could use [`adapter-static`](https://github.com/sveltejs/kit/tree/master/packages/adapter-static), which will generate HTML files for every page, plus additional files that are requested by `load` functions in those pages.
アプリ全体がprerenderするのに適しているなら、[`adapter-static`](https://github.com/sveltejs/kit/tree/master/packages/adapter-static)を使うことが出来るでしょう。`adapter-static`は、各ページのHTMLファイルに加えて、それらのページの`load`関数からリクエストされる付加的なファイルを生成します。

In many cases, you'll only want to prerender specific pages in your app. You'll need to annotate these pages:
多くの場合、アプリの中の特定のページをprerenderしたいだけでしょう。それらのページに以下のように追記する必要があります。

```html
<script context="module">
	export const prerender = true;
</script>
```

The prerenderer will start at the root of your app and generate HTML for any prerenderable pages it finds. Each page is scanned for `<a>` elements that point to other pages that are candidates for prerendering — because of this, you generally don't need to specify which pages should be accessed. If you _do_ need to specify which pages should be accessed by the prerenderer, you can do so with the `pages` option in the [prerender configuration](#configuration-prerender).
prerender機能は、アプリのルートで開始し、prerender可能と判断したページに対するHTMLを生成します。prerenderする候補となる他のページを指す`<a>`要素を得るためにスキャンされます。このおかげで、どのページがアクセスされるべきか指定する必要は一般的にはありません。もしどのページがアクセスされるべきか指定する必要が*ある*なら、[prerenderの設定](#configuration-prerender)の`pages`オプションで指定出来ます。

#### When not to prerender

The basic rule is this: for a page to be prerenderable, any two users hitting it directly must get the same content from the server.
基本的なルールはこうです。あるページがprerender可能であるには、そのページに直接辿り着いたいかなる2人のユーザーも同じ内容をサーバーから得るべし。

> In other words, any app that involves user sessions or authentication is _not_ a candidate for `adapter-static`, even if individual pages within an app _are_ suitable for prerendering.
> 言い換えると、アプリ内の個別のページがprerenderに適していても、ユーザーセッションや認証が絡むいかなるアプリも`adapter-static`の候補では*ありません*。

Note that you can still prerender pages that load data based on the page's parameters, like our `src/routes/blog/[slug].svelte` example from earlier. The prerenderer will intercept requests made inside `load`, so the data served from `src/routes/blog/[slug].json.js` will also be captured.
以前の`src/routes/blog/[slug].svelte`の例のように、ページのパラメーターに基づいてデータを読み込むページであればprerender出来ることに注意してください。prerender機能は`load`の中でxxxx

#### Route conflicts

Because prerendering writes to the filesystem, it isn't possible to have two endpoints that would cause a directory and a file to have the same name. For example, `src/routes/foo/index.js` and `src/routes/foo/bar.js` would try to create `foo` and `foo/bar`, which is impossible.
prerender機能は、ファイルシステムへの書き込みをするので、ディレクトリとファイルが同じ名前を持つことになってしまう2つのエンドポイントを持つことは不可能です。例えば、`src/routes/foo/index.js`と`src/routes/foo/bar.js`は、`foo`と`foo/bar`を作ることになりますが、これは不可能です。

For that reason among others, it's recommended that you always include a file extension — `src/routes/foo/index.json.js` and `src/routes/foo/bar.json.js` would result in `foo.json` and `foo/bar.json` files living harmoniously side-by-side.
ファイル拡張子を常に入れておくことを推奨します。`src/routes/foo/index.json.js`と`src/routes/foo/bar.json.js`であれば、`foo.json`と`foo/bar.json`が隣同士に共存することになります。

For _pages_, we skirt around this problem by writing `foo/index.html` instead of `foo`.
*ページ*に対しては、`foo/index.html`の代わりに`foo`と書くことでこの問題を回避します。
