---
title: Command Line Interface
---

SvelteKit includes a command line interface for building and running your app.
SvelteKitには、アプリをビルドしたり実行するためのコマンドラインインターフェースがあります。

In the default project template `svelte-kit dev`, `svelte-kit build` and `svelte-kit start` are aliased as `npm run dev`, `npm run build` and `npm start` respectively. You can also invoke the CLI with [npx](https://www.npmjs.com/package/npx):
デフォルトのプロジェクトテンプレートでは、`svelte-kit dev`、`svelte-kit build`、`svelte-kit start`はそれぞれ、`npm run dev`、`npm run build`、`npm start`にエイリアスされています。CLIを[npx](https://www.npmjs.com/package/npx)で呼び出すこともできます。

```bash
npx svelte-kit dev
```

### svelte-kit dev

Starts a development server. It accepts the following options:
開発サーバーを起動します。以下のオプションを受け付けます。

- `-p`/`--port` — which port to start the server on
- `-p`/`--port` — どのポートでサーバーを起動するか
- `-o`/`--open` — open a browser tab once the server starts
- `-o`/`--open` — サーバーが起動したらブラウザを開く
- `-h`/`--host` — expose the server to the network. This will allow people using the same coffee shop WiFi as you to see files on your computer; use it with care
- `-h`/`--host` — サーバーをネットワークに後悔する。同じコーヒーショップのWiFi利用者が自分のコンピューター上のファイルを閲覧できるようになる。注意して使うこと。
- `-H`/`--https` — launch an HTTPS server using a self-signed certificate. Useful for testing HTTPS-only features on an external device
- `-H`/`--https` — 自己署名証明書を使ってHTTPSサーバーを起動する。xxon an external devicexxHTTPSのみの機能をテストするために有益

### svelte-kit build

Builds a production version of your app, and runs your adapter if you have one specified in your [config](#configuration). It accepts the following option:
アプリのプロダクション版をビルドし、[設定](#configuration)でアダプターを指定していればそのアダプターを実行します。以下のオプションを受け付けます。

- `--verbose` — log more detail
- `--verbose` — 詳細なログを出力する

### svelte-kit start

After you've built your app with `svelte-kit build`, you can start the production version (irrespective of any adapter that has been applied) locally with `svelte-kit start`. This is intended for testing the production build locally, **not for serving your app**, for which you should always use an adapter.
`svelte-kit build`でアプリをビルドしたら、`svelte-kit start`によってプロダクション版を（適用されているアダプターと無関係に）ローカルで起動できます。

Like `svelte-kit dev`, it accepts the following options:
`svelte-kit dev`と同じく以下のオプションを受け付けます。

- `-p`/`--port`
- `-o`/`--open`
- `-h`/`--host` (note the security caveat [above](#command-line-interface-svelte-kit-dev))
- `-h`/`--host` （[上記](#command-line-interface-svelte-kit-dev))のセキュリティの欠点に注意すること）
- `-H`/`--https`
