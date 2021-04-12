---
title: Routing
---

At the heart of SvelteKit is a _filesystem-based router_. This means that the structure of your application is defined by the structure of your codebase — specifically, the contents of `src/routes`.
SvelteKitの中心には、ファイルシステムベースのルーターがあります。つまり、アプリケーションの構造は、コードベースの構造（正確には`src/routes`の内容）によって定義されます。

> You can change this to a different directory by editing the [project config](#configuration).
> [project config](#configuration)を編集することでこれを別のディレクトリに変更することが出来ます。

There are two types of route — **pages** and **endpoints**.
ルートには、**ページ**と**エンドポイント**の2種類があります。

Pages typically generate HTML to display to the user (as well as any CSS and JavaScript needed for the page). By default pages are rendered on both the client and server though this behavior is configurable.
ページはユーザーに表示されるHTML（およびそのページに必要なCSSとJavaScript）を生成します。ページは、デフォルトで、クライアントとサーバーのどちらの上でも描画されます。この挙動は設定可能です。

Endpoints run only on the server (or when you build your site, if [prerendering](#ssr-and-javascript-prerender)). This means it's the place to do things like access databases or APIs that require private credentials or return data that lives on a machine in your production network. Pages can request data from endpoints. Endpoints return JSON by default though may also return data in other formats.
エンドポイントはサーバー上で（あるいは、[prerendering](#ssr-and-javascript-prerender)の場合はサイトの構築時）のみ動作します。エンドポイントが、プライベートなクレデンシャルを必要としたり、本番ネットワークのマシン上に置いておくデータを返すデータベースやAPIへのアクセスをするべき場所ということです。ページはエンドポイントからのデータをリクエストすることが出来ます。エンドポイントは、デフォルトでJSONを返しますが、他の形式のデータを返すこともあり得ます。

### Pages

Pages are Svelte components written in `.svelte` files (or any file with an extension listed in [`config.extensions`](#configuration)). By default, when a user first visits the application, they will be served a server-rendered version of the page in question, plus some JavaScript that 'hydrates' the page and initialises a client-side router. From that point forward, navigating to other pages is handled entirely on the client for a fast, app-like feel where the common portions in the layout do not need to be rerendered.
ページは、`.svelte`ファイル（あるいは[`config.extensions`](#configuration))にあげられている任意の拡張子のファイル）に書かれたSvelteコンポーネントです。デフォルトでは、ユーザーが最初にアプリケーションを訪れると、該当ページのサーバーでレンダーされたバージョンと、そのページを「xxhydratesxx」し、クライアント側のルーターを初期化するJavaScriptが提供されます。そこから先は、他のページへの遷移は、レイアウトの共通部分が再度レンダリングされる必要のない高速でアプリらしい感じがするように、完全にクライアント上で処理されます。

The filename determines the route. For example, `src/routes/index.svelte` is the root of your site:
ルート（route）はファイル名によって決まります。例えば、`src/routes/index.svelte`はサイトのルート（root）です。

```html
<!-- src/routes/index.svelte -->
<svelte:head>
	<title>Welcome</title>
</svelte:head>

<h1>Hello and welcome to my site!</h1>
```

A file called either `src/routes/about.svelte` or `src/routes/about/index.svelte` would correspond to the `/about` route:
`src/routes/about.svelte`または`src/routes/about/index.svelte`というファイルがあれば`/about`ルートに対応することになります。

```html
<!-- src/routes/about.svelte -->
<svelte:head>
	<title>About</title>
</svelte:head>

<h1>About this site</h1>
<p>TODO...</p>
```

Dynamic parameters are encoded using `[brackets]`. For example, a blog post might be defined by `src/routes/blog/[slug].svelte`. Soon, we'll see how to access that parameter in a [load function](#loading) or the [page store](#modules-app-stores).
動的なパラメーターは`[brackets]`を用いてエンコードされます。例えば、ブログポストは`src/routes/blog/[slug].svelte`で定義され得るでしょう。
そのようなパラメーターの取得方法は、すぐに[load function](#loading)または[page store](#modules-app-stores)で見ることになります。

### Endpoints

Endpoints are modules written in `.js` (or `.ts`) files that export functions corresponding to HTTP methods. For example our hypothetical blog page, `/blog/cool-article`, might request data from `/blog/cool-article.json`, which could be represented by a `src/routes/blog/[slug].json.js` endpoint:
エンドポイントは、`.js`（または`.ts`）ファイルに書かれた、HTTPメソッドに対応する関数をエクスポートするモジュールです。例えば、仮にブログページ`/blog/cool-article`があったとして、`/blog/cool-article.json`にデータをリクエストするとします。これは、エンドポイント`src/routes/blog/[slug].json.js`で表されることがありえるでしょう。

```ts
type Request<Context = any> = {
	host: string;
	method: 'GET';
	headers: Record<string, string>;
	path: string;
	params: Record<string, string | string[]>;
	query: URLSearchParams;
	body: string | Buffer | ReadOnlyFormData;
	context: Context; // see getContext, below
};

type Response = {
	status?: number;
	headers?: Record<string, string>;
	body?: any;
};

type RequestHandler<Context = any> = {
	(request: Request<Context>) => Response | Promise<Response>;
}
```

```js
import db from '$lib/database';

/**
 * @type {import('@sveltejs/kit').RequestHandler}
 */
export async function get({ params }) {
	// the `slug` parameter is available because this file
	// is called [slug].json.js
	const { slug } = params;

	const article = await db.get(slug);

	if (article) {
		return {
			body: {
				article
			}
		};
	}
}
```

> Returning nothing is equivalent to an explicit 404 response.
> 何も返さないことは明示的な404のレスポンスと同じです。

The job of this function is to return a `{status, headers, body}` object representing the response. If the returned `body` is an object, and no `content-type` header is returned, it will automatically be turned into a JSON response. (Don't worry about `$lib`, we'll get to that [later](#$lib).)
この関数の仕事は、レスポンスを表すオブジェクト`{status, headers, body}`を返すことです。返された`body`がオブジェクトでありかつ`content-type`ヘッダーが返されない場合、自動的にJSONレスポンスに変換されます。（`$lib`について不安にならないように。[後ほど](#$lib)理解することになるでしょう。）

For endpoints that handle other HTTP methods, like POST, export the corresponding function:
他のHTTPメソッドを処理するエンドポイントにするは、対応する関数をエクスポートしてください。

```js
export function post(request) {...}
```

Since `delete` is a reserved word in JavaScript, DELETE requests are handled with a `del` function.
`delete`はJavaScriptでは予約語なので、DELETEリクエストは`del`関数で処理されます。

> We don't interact with the `req`/`res` objects you might be familiar with from Node's `http` module or frameworks like Express, because they're only available on certain platforms. Instead, SvelteKit translates the returned object into whatever's required by the platform you're deploying your app to.
> Nodeの`http`モジュールやExpressのようなフレームワークで慣れ親しんでいるかもしれませんが、`req`/`res`オブジェクトを操作しません。なぜなら、これらは限られたプラットフォームでのみ利用可能だからです。代わりにSvelteKitは返されたオブジェクトを、アプリをデプロイしたプラットフォームが必要としているものに変換します。
>
> The `body` property of the request object exists in the case of POST requests. If you're posting form data, it will be a read-only version of the [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) object.
> POSTリクストの場合には、リクエストオブジェクトの`body`プロパティが存在します。フォームデータをポストしている場合、読み取り専用の[`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData)になります。

To set multiple cookies in a single set of response headers, you can return an array:
複数のクッキーを1つのレスポンスヘッダーにまとめるために、配列を返すことが出来ます。

```js
return {
	headers: {
		'set-cookie': [cookie1, cookie2]
	}
};
```

### Private modules

A filename that has a segment with a leading underscore, such as `src/routes/foo/_Private.svelte` or `src/routes/bar/_utils/cool-util.js`, is hidden from the router, but can be imported by files that are not.
`src/routes/foo/_Private.svelte`や`src/routes/bar/_utils/cool-util.js`のように、先頭にアンダースコアのあるセグメントを含むファイル名は、ルーターから隠されますが、隠されていないファイルによってインポートされることは可能です。

### Advanced

#### Rest parameters

A route can have multiple dynamic parameters, for example `src/routes/[category]/[item].svelte` or even `src/routes/[category]-[item].svelte`. If the number of route segments is unknown, you can use rest syntax — for example you might implement GitHub's file viewer like so...
ルートは、複数の動的なパラメーター（例えば、`src/routes/[category]/[item].svelte`あるいは、`src/routes/[category]-[item].svelte`でさえ）を持つことが出来ます。ルートのセグメント数が分からない場合、rest構文を使うことが出来ます。例えば、GitHubのファイルビューワーは以下のように実装する可能性があるでしょう。

```bash
/[org]/[repo]/tree/[branch]/[...file]
```

...in which case a request for `/sveltejs/kit/tree/master/documentation/docs/01-routing.md` would result in the following parameters being available to the page:
この場合、`/sveltejs/kit/tree/master/documentation/docs/01-routing.md`へのリクエストは、結果的に、以下のパラメーターがページに対して利用可能になるでしょう。

```js
{
	org: 'sveltejs',
	repo: 'kit',
	branch: 'master',
	file: 'documentation/docs/01-routing.md'
}
```

> `src/routes/a/[...rest]/z.svelte` will match `/a/z` as well as `/a/b/z` and `/a/b/c/z` and so on. Make sure you check that the value of the rest parameter is valid.
> `src/routes/a/[...rest]/z.svelte`は、`/a/b/z`、`/a/b/c/z`などに加えて`/a/z`にもマッチします。restパラメーターが妥当であることをきちんと確認してください。

#### Fallthrough routes

Finally, if you have multiple routes that match a given path, SvelteKit will try each of them until one responds. For example if you have these routes...
最後に、あるパスにマッチするルートがもし複数あれば、SvelteKitは、どれか1つが対応するまでそれぞれのルートを試すことになります。例えば、以下の3つのルートがあるとします。

```bash
src/routes/[baz].js
src/routes/[baz].svelte
src/routes/[qux].svelte
src/routes/foo-[bar].svelte
```

...and you navigate to `/foo-xyz`, then SvelteKit will first try `foo-[bar].svelte` because it is the best match, then will try `[baz].js` (which is also a valid match for `/foo-xyz`, but less specific), then `[baz].svelte` and `[qux].svelte` in alphabetical order (endpoints have higher precedence than pages). The first route that responds — a page that returns something from [`load`](#loading) or has no `load` function, or an endpoint that returns something — will handle the request.
そして、`/foo-xyz`に移動すると、SvelteKitは、最もマッチしているのでまず`foo-[bar].svelte`を試します。次に（同じく正しくマッチはするものの、xxless specificxx）`[baz].js`を試し、その後で、`[baz].svelte`と`[qux].svelte`をアルファベット順を試します（エンドポイントはページよりも優先順位が高いです）。
最初に対応するルート（[`load`](#loading)から何かを返すか`load`関数を持たないページ、あるいは何かを返すエンドポイント）がリクエストを処理することになります。

If no page or endpoint responds to a request, SvelteKit will respond with a generic 404.
どのページもエンドポイントもリクエストに対応しない場合、SvelteKitが汎用の404で対応することになります。
