---
title: Service workers
---

Service workers act as proxy servers that handle network requests inside your app. This makes it possible to make your app work offline, but even if you don't need offline support (or can't realistically implement it because of the type of app you're building), it's often worth using service workers to speed up navigation by precaching your built JS and CSS.
service workerは、アプリの中でネットワークリクエストを処理するプロキシサーバーとしての役割を果たします。これによってアプリがオフラインで動作することが可能になりますが、オフラインのサポートが必要なくても（あるいは、オフラインサポートの実装が現実的でなくても）、構築済みのJSとCSSを事前にキャッシュすることで遷移を高速化するためにservice workerを使う価値はあります。

In SvelteKit, if you have a `src/service-worker.js` file (or `src/service-worker.ts`, or `src/service-worker/index.js`, etc) it will be built with Vite and automatically registered.
SvelteKitでは、`src/service-worker.js`（あるいは`src/service-worker.ts`や`src/service-worker/index.js`など）があると、Viteでビルドされて自動的に登録されます。

> You can change the location of your service worker in your [project configuration](#configuration-files).
> [プロジェクトの設定](#configuration-files)でservice workerの場所を変更できます。

Inside the service worker you have access to the [`$service-worker` module](#modules-service-worker).
service workerの中では、[`$service-worker`モジュール](#modules-service-worker)が利用できます。

Because it needs to be bundled (since browsers don't yet support `import` in this context), and depends on the client-side app's build manifest, **service workers only work in the production build, not in development**. To test it locally, use [`svelte-kit start`](#command-line-interface-svelte-kit-start).
ブラウザがまだ`import`をサポートしておらず、バンドルされる必要があり、クライアント側のアプリのビルドマニフェストに依存しているので、**service workerはproductionビルドでのみ機能し、developmentでは機能しません**。ローカルでテストするには、[`svelte-kit start`](#command-line-interface-svelte-kit-start)を利用してください。