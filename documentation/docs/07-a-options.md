---
title: Anchor options
---

### sveltekit:prefetch

SvelteKit uses code splitting to break your app into small chunks (one per route), ensuring fast startup times.
SvelteKitはアプリを（ルートにつき1つの）小さなまとまりに分割するためにcode splittingを使い、高速な起動を確実にしています。

For _dynamic_ routes, such as our `src/routes/blog/[slug].svelte` example, that's not enough. In order to render the blog post, we need to fetch the data for it, and we can't do that until we know what `slug` is. In the worst case, that could cause lag as the browser waits for the data to come back from the server.
`src/routes/blog/[slug].svelte`の例のようなダイナミックルートに対しては、それでは不十分です。このブログポストを表示するには、そのためのデータを取得する必要があり、`slug`が何かわからないうちにxxxxはできません。最悪の場合、データがサーバーから帰ってくるのをブラウザが待つために、遅れを引き起こしかねません。

We can mitigate that by _prefetching_ the data. Adding a `sveltekit:prefetch` attribute to a link...
データを*プリフェッチ*することでこの問題を緩和出来ることがあります。リンクに`sveltekit:prefetch`属性を追加すると…

```html
<a sveltekit:prefetch href="blog/what-is-sveltekit">What is SvelteKit?</a>
```

...will cause SvelteKit to run the page's `load` function as soon as the user hovers over the link (on a desktop) or touches it (on mobile), rather than waiting for the `click` event to trigger navigation. Typically, this buys us an extra couple of hundred milliseconds, which is the difference between a user interface that feels laggy, and one that feels snappy.
ユーザーが（デスクトップ上で）リンクの上にカーソルを置くか、（モバイルで）リンクにタッチするとすぐに、SvelteKitがそのページの`load`関数を実行するようになります。

Note that prefetching will not work if the [`router`](#ssr-and-javascript-router) setting is `false`.
プリフェッチングは[`router`](#ssr-and-javascript-router)が`false`に設定されていると動かないことに注意してください。

You can also programmatically invoke `prefetch` from `$app/navigation`.
`$app/navigation`の`prefetch`をプログラムから呼び出すこともできます。

### sveltekit:noscroll

When navigating to internal links, SvelteKit mirrors the browser's default navigation behaviour: it will change the scroll position to 0,0 so that the user is at the very top left of the page (unless the link includes a `#hash`, in which case it will scroll to the element with a matching ID).
内部リンクに遷移するとき、SvelteKitはブラウザのデフォルトの遷移の挙動を真似します。ユーザーがページの左上に位置するように、スクロール位置を0,0に変更します（これはリンクが`#hash`を含まなければの話で、含む場合はマッチするIDの要素までスクロールします）。

In certain cases, you may wish to disable this behaviour. Adding a `sveltekit:noscroll` attribute to a link...
この挙動を無効にしたい場合もあるかもしれません。リンクに`sveltekit:noscroll`属性を追加すると…

```html
<a href="path" sveltekit:noscroll>Path</a>
```

...will prevent scrolling after the link is clicked.
リンクがクリックされた後にスクロールしないようになります。

### rel=external

By default, the SvelteKit runtime intercepts clicks on `<a>` elements and bypasses the normal browser navigation for relative (same-origin) URLs that match one of your page routes. We sometimes need to tell SvelteKit that certain links need to be handled by normal browser navigation.
SvelteKitのランタイムは、`<a>`要素のクリックを捕捉し、相対的な（同一オリジンの）URLでページのルートにマッチするものへの通常のブラウザーの遷移を迂回します。特定のリンクが通常のブラウザの遷移によって処理されなければならないことをSvelteKitに伝える必要があることもあります。

Adding a `rel=external` attribute to a link...
リンクに`rel=external`属性を追加すると…

```html
<a rel="external" href="path">Path</a>
```

...will trigger a browser navigation when the link is clicked.
リンクがクリックされたときにブラウザの遷移が引き起こされるようになります。