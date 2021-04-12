---
title: Loading
---

A component that defines a page can export a `load` function that runs before the component is created. This function runs both during server-side rendering and in the client, and allows you to get data for a page without (for example) showing a loading spinner and fetching data in `onMount`.
ページを定義するコンポーネントは、そのコンポーネントが作成される前に動作する`load`関数をエクスポートすることが出来ます。この関数は、サーバーサイドレンダリング中とクライアントの中の両方で動作します。
例えば読み込みのぐるぐるを表示することなくデータが取得できたり、xxxx

Our example blog page might contain a `load` function like the following. Note the `context="module"` — this is necessary because `load` runs before the component is rendered:
ブログページの例は、以下のような`load`関数を含んでいます。`context="module"`に注意してください。これが必要なのは、コンポーネントが描画される前に`load`が動作するからです。


```ts
type LoadInput = {
	page: {
		host: string;
		path: string;
		params: Record<string, string | string[]>;
		query: URLSearchParams;
	};
	fetch: (info: RequestInfo, init?: RequestInit) => Promise<Response>;
	session: any;
	context: Record<string, any>;
};

type LoadOutput = {
	status?: number;
	error?: Error;
	redirect?: string;
	props?: Record<string, any>;
	context?: Record<string, any>;
	maxage?: number;
};
```

```html
<script context="module">
	/**
	 * @type {import('@sveltejs/kit').Load}
	 */
	export async function load({ page, fetch, session, context }) {
		const url = `/blog/${page.params.slug}.json`;
		const res = await fetch(url);

		if (res.ok) {
			return {
				props: {
					article: await res.json()
				}
			};
		}

		return {
			status: res.status,
			error: new Error(`Could not load ${url}`)
		};
	}
</script>
```

`load` is similar to `getStaticProps` or `getServerSideProps` in Next.js, except that it runs on both the server and the client.
`load`は、サーバーとクライアントの両方で動作するという点以外、Next.jsの`getStaticProps`や`getServerSideProps`に似ています。

If `load` returns nothing, SvelteKit will [fall through](#routing-advanced-fallthrough-routes) to other routes until something responds, or will respond with a generic 404.
`load`が何も返さないと、SvelteKitは対応するものが現れるまで他のルートを[fall through](#routing-advanced-fallthrough-routes)し、対応するものがなければ、汎用の404で対応します。

> `load` only applies to components that define pages, not the components that they import.
> `load`はページを定義するコンポーネントにだけ適用され、それがインポートするコンポーネントには適用されません。

### Input

The `load` function receives an object containing four fields — `page`, `fetch`, `session` and `context`.
`load`関数は、4つのフィールド（`page`、`fetch`、`session`、`context`）を含むオブジェクトを受け取ります。

#### page

`page` is a `{ host, path, params, query }` object where `host` is the URL's host, `path` is its pathname, `params` is derived from `path` and the route filename, and `query` is an instance of `URLSearchParams`.
`page`は、`{ host, path, params, query }`オブジェクトで、`host`はURLのホスト、`path`はそのpathname、`params`は`path`とルートのファイル名から作られ、`query`は`URLSearchParams`のインスタンスです.

So if the example above was `src/routes/blog/[slug].svelte` and the URL was `/blog/some-post?foo=bar&baz`, the following would be true:
上記の例が仮に`src/routes/blog/[slug].svelte`、URLが`/blog/some-post?foo=bar&baz`だとすると、以下のことがtrueになるでしょう。

- `page.path === '/blog/some-post'`
- `page.params.slug === 'some-post'`
- `page.query.get('foo') === 'bar'`
- `page.query.has('baz')`

#### fetch

`fetch` is equivalent to the native `fetch` web API, and can make credentialled requests. It can be used across both client and server contexts.
`fetch`はネイティブの`fetch`Web APIと等しく、クライアントとサーバーのどちらのコンテキストの中でも使うことが出来ます。

> When `fetch` runs on the server, the resulting response will be serialized and inlined into the rendered HTML. This allows the subsequent client-side `load` to access identical data immediately without an additional network request.
> `fetch`がサーバー上で動作すると、結果のレスポンスはシリアライズされ、レンダリングされたHTMLにxxinlinexxされます。これによって後続のクライアント側の`load`が、追加のネットワークリクエストなしに、同一のデータにすぐアクセスできるようになります。

#### session

`session` can be used to pass data from the server related to the current request, e.g. the current user. By default it is `undefined`. See [`getSession`](#hooks-getsession) to learn how to use it.
`session`は、現在のリクエストに関連するサーバーからのデータ（例えば、カレントユーザー）を渡すために使うことが出来ます。デフォルトでは、`undefined`です。使い方を学ぶには[`getSession`](#hooks-getsession)を参照してください。

#### context

`context` is passed from layout components to child layouts and page components. For the root `$layout.svelte` component, it is equal to `{}`, but if that component's `load` function returns an object with a `context` property, it will be available to subsequent `load` functions.
`context`は、レイアウトコンポーネントから子のレイアウトとページコンポーネントに渡されます。ルートの`$layout.svelte`に対しては、`{}`と同じですが、`context`プロパティを持つオブジェクトを`load`関数が返すと、xxxx

> It is important to note that `load` may run on either the server or in the client browser. Code called inside `load` blocks:
> `load`はサーバー上またはクライアント内のいずれかで動作し得ることに注意することが重要です。`load`ブロック内で呼び出されたコードは、
>
> - should run on the same domain as any upstream API servers requiring credentials
> - クレデンシャルを必要とする上流のAPIサーバーと同じドメインで動作しなければいけません
> - should not reference `window`, `document` or any browser-specific objects
> - `window`や`document`、その他いかなるブラウザ特有のオブジェクトも参照してはいけません
> - should not reference any API keys or secrets, which will be exposed to the client
> - いかなるAPIキーやシークレットも参照してはいけません。クライアントに公開されることになります

### Output

If you return a Promise from `load`, SvelteKit will delay rendering until the promise resolves. The return value has several properties, all optional:
`load`からPromiseを返す場合、SvelteKitはそのプロミスがresolveするまで、描画を遅延させます。戻り値はいくつかのプロパティを持っており、全てオプションです。

#### status

The HTTP status code for the page. If returning an `error` this must be a `4xx` or `5xx` response; if returning a `redirect` it must be a `3xx` response. The default is `200`.
ページのHTTPステータスコード。もし`error`を返すなら、これは`4xx`あるいは`5xx`のレスポンスでなければなりません。もし`redirect`を返すなら、これは`3xx`でなければなりません。デフォルトは`200`です。

#### error

If something goes wrong during `load`, return an `Error` object or a `string` describing the error alongside a `4xx` or `5xx` status code.
`load`の中で何かうまく行かなかったら、`4xx`または`5xx`ステータスコードに加えて、`Error`オブジェクトまたはエラーを説明する`string`を返してください。

#### redirect

If the page should redirect (because the page is deprecated, or the user needs to be logged in, or whatever else) return a `string` containing the location to which they should be redirected alongside a `3xx` status code.
もしページが（古かったり、ユーザーがログインする必要があったり、その他何らかの理由で）リダイレクトするべきなら、`3xx`のステータスコードに加えて、リダイレクトするべき先の場所を含む`string`を返してください。

#### maxage

To cause pages to be cached, return a `number` describing the page's max age in seconds. The resulting cache header will include `private` if user data was involved in rendering the page (either via `session`, or because a credentialled `fetch` was made in a `load` function), but otherwise will include `public` so that it can be cached by CDNs.
ページがキャッシュされるようにするには、ページのmax ageを秒で表す`number`を返してください。結果となるキャッシュヘッダーは`private`を含みます。ページをレンダリングするのにユーザーデータが（`session`経由または）関わっているのなら

This only applies to page components, _not_ layout components.
これはページコンポーネントのみに適用され、レイアウトコンポーネントには適用*されません*。

#### props

If the `load` function returns a `props` object, the props will be passed to the component when it is rendered.
`load`関数が`props`オブジェクトを返すと、コンポーネントが描画されるときに渡されます。

#### context

This will be merged with any existing `context` and passed to the `load` functions of subsequent layout and page components.
後続のレイアウトとページコンポーネントの`load`関数に渡されます。

This only applies to layout components, _not_ page components.
これはレイアウトコンポーネントのみに適用され、ページコンポーネントには適用*されません*。
