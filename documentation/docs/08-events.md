---
title: Events
---

SvelteKit emits [CustomEvents](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent) on the `window` object when certain things happen:
SvelteKitは、特定のことが起きたときに、[CustomEvents](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)を`window`オブジェクトに発生させます。

* `sveltekit:start` — fired once the app has hydrated
* `sveltekit:start` — アプリがhydrateした
* `sveltekit:navigation-start` — navigation has started
* `sveltekit:navigation-start` — 遷移が開始した
* `sveltekit:navigation-end` — navigation has ended
* `sveltekit:navigation-end` — 遷移が終了した

You probably won't need to use these, but they can be useful in the context of (for example) integration tests.
おそらくこれらを使う必要は出てこないですが、例えば結合テストなどをする状況で役に立つかもしれません。