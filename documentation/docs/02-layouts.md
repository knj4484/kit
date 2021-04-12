---
title: Layouts
---

So far, we've treated pages as entirely standalone components — upon navigation, the existing component will be destroyed, and a new one will take its place.
ここまで、ページを完全に独立したコンポーネントとして扱ってきました。遷移すると、存在しているコンポーネントは破壊され、新しいコンポーネントがとって変わります。

But in many apps, there are elements that should be visible on _every_ page, such as top-level navigation or a footer. Instead of repeating them in every page, we can use _layout_ components.
しかし、多くのアプリでは、全てのページで見ることになるはずの要素（例えば、トップレベルのナビゲーションやフッター）があります。全てのページでそれらを繰り返す代わりに、 *レイアウト*コンポーネントを使うことが出来ます。

To create a layout component that applies to every page, make a file called `src/routes/$layout.svelte`. The default layout component (the one that SvelteKit uses if you don't bring your own) looks like this...
全てのページに適用されるレイアウトコンポーネントを作成するには、`src/routes/$layout.svelte`というファイルを作ってください。デフォルトのレイアウトコンポーネント（独自のものを導入しなければSvelteKitが使用するもの）は、以下のようになります。

```html
<slot></slot>
```

...but we can add whatever markup, styles and behaviour we want. For example, let's add a nav bar:
望みのどんなマークアップ、スタイル、振る舞いでも追加できます。例えば、ナビゲーションバーを追加してみましょう。

```html
<!-- src/routes/$layout.svelte -->
<nav>
	<a href=".">Home</a>
	<a href="about">About</a>
	<a href="settings">Settings</a>
</nav>

<slot></slot>
```

If we create pages for `/`, `/about` and `/settings`...
`/`、`/about`、`/settings`のためのページを以下のように作成すると、

```html
<!-- src/routes/index.svelte -->
<h1>Home</h1>
```

```html
<!-- src/routes/about.svelte -->
<h1>About</h1>
```

```html
<!-- src/routes/settings.svelte -->
<h1>Settings</h1>
```

...the nav will always be visible, and clicking between the three pages will only result in the `<h1>` being replaced.
navが常に見えるようになり、3つのページ間のクリックによって`<h1>`が置き換えられるだけになります。

### Nested layouts

Suppose we don't just have a single `/settings` page, but instead have nested pages like `/settings/profile` and `/settings/notifications` with a shared submenu (for a real-life example, see [github.com/settings](https://github.com/settings)).
単一の`/settings`ページだけがあるのではなく、共通のサブメニューを持つ`/settings/profile`と`/settings/notifications`のような入れ子のページがあると想定してください（実世界の例として、[github.com/settings](https://github.com/settings)を見てください）。

We can create a layout that only applies to pages below `/settings` (while inheriting the root layout with the top-level nav):
（トップレベルのnavがあるルートレイアウトを引き継ぎつつ）`/settings`以下のページにだけ適用されるレイアウトを以下のように作成することが出来ます。

```html
<!-- src/routes/settings/$layout.svelte -->
<h1>Settings</h1>

<div class="submenu">
	<a href="/settings/profile">Profile</a>
	<a href="/settings/notifications">Notifications</a>
</div>

<slot></slot>
```

### Error pages

If a page fails to load (see [Loading](#loading)), SvelteKit will render an error page. You can customise this page by creating `$error.svelte` components alongside your layout and page components.
ページのロード（[Loading](#loading)を参照）に失敗すると、SvelteKitはエラーページを表示します。レイアウトとページコンポーネントに加えて`$error.svelte`コンポーネントを作成することで、このページをカスタマイズすることが出来ます。

For example, if `src/routes/settings/notifications/index.svelte` failed to load, SvelteKit would render `src/routes/settings/notifications/$error.svelte` in the same layout, if it existed. If not, it would render `src/routes/settings/$error.svelte` in the parent layout, or `src/routes/$error.svelte` in the root layout.
例えば、仮に`src/routes/settings/notifications/index.svelte`がロードに失敗したすると、SvelteKitは、`src/routes/settings/notifications/$error.svelte`が存在すれば、それを同じレイアウトの中にレンダリングすることになるでしょう。存在しなければ、親レイアウトの中に`src/routes/settings/$error.svelte`をレンダリングするか、ルートレイアウトの中に`src/routes/$error.svelte`をレンダリングすることになるでしょう。

> SvelteKit provides a default error page in case you don't supply `src/routes/$error.svelte`, but it's recommend that you bring your own.
> SvelteKitは、`src/routes/$error.svelte`を作成しない場合には、デフォルトのエラーページを提供しますが、自前のエラーページを用意することを推奨します。

If an error component has a [`load`](#loading) function, it will be called with `error` and `status` properties:
エラーコンポーネントに[`load`](#loading)関数がある場合、`error`と`status`プロパティを伴って呼び出されることになります。

```html
<script context="module">
	export function load({ error, status }) {
		return {
			props: {
				title: `${status}: ${error.message}`
			}
		};
	}
</script>

<script>
	export let title;
</script>

<h1>{title}</h1>
```

> Server-side stack traces will be removed from `error` in production, to avoid exposing privileged information to users.
> 本番環境では、特権が必要な情報をユーザーに公開してしまうのを避けるために、`error`からサーバーサイドのスタックトレースが削除されます。
