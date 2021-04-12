---
title: Introduction
---

### Before we begin

> SvelteKit is in early development, and some things may change before we hit version 1.0. This document is a work-in-progress. If you get stuck, reach out for help in the [Discord chatroom](https://svelte.dev/chat).
> SvelteKitは開発初期段階にあり、バージョン1.0に到達する前に変わることがありえます。このドキュメントは、作成中の未完成品です。もし、行き詰まったら、[Discordのチャットルーム](https://svelte.dev/chat)で助けを求めてください。
>
> See the [migration guides](migrating) for help upgrading from Sapper.
> Sapperからのアップグレードxxxx[マイグレーションガイド](migrating)を参照してください。

### What is SvelteKit?
SvelteKitとは何か？

SvelteKit is a framework for building extremely high-performance web apps. You're looking at one right now! There are two basic concepts:
SvelteKitは、非常に高性能なWebアプリを構築するためのフレームワークです。まさに今見ているものがその一つです！基本的なコンセプトは以下の2つです。

* Each page of your app is a [Svelte](https://svelte.dev) component
* アプリの各ページは[スベルト](https://svelte.dev)コンポーネントです
* You create pages by adding files to the `src/routes` directory of your project. These will be server-rendered so that a user's first visit to your app is as fast as possible, then a client-side app takes over
* プロジェクトの`src/routes`ディレクトリにファイルを追加することでページを作成します。ユーザーが最初に訪問するときに可能な限り速くなるようにサーバー側でレンダーされることになり、その後はクライアント側のアプリが引き継ぎます

Building an app with all the modern best practices — code-splitting, offline support, server-rendered views with client-side hydration — is fiendishly complicated. SvelteKit does all the boring stuff for you so that you can get on with the creative part.
モダンなベストプラクティス（コードの分割、オフラインのサポート、サーバーでレンダーされるビューxxwithxxクライアントサイドでのハイドレーション）全てを使って構築することは、残酷なほどに複雑です。SvelteKitが退屈なことを全てしてくれるので、創造的な部分を続けることが出来ます。

You don't need to know Svelte to understand the rest of this guide, but it will help. In short, it's a UI framework that compiles your components to highly optimized vanilla JavaScript. Read the [introductory blog post](https://svelte.dev/blog/svelte-3-rethinking-reactivity) and the [tutorial](https://svelte.dev/tutorial) to learn more.
このガイドの残りを理解するためにSvelteを知っている必要はありませんが、知っていれば理解の助けになるでしょう。簡単にいうと、Svelteは、コンポーネントを高度に最適化された純粋なJavaScriptにコンパイルするUIフレームワークです。さらに学習するには、[導入となるブログポスト](https://svelte.dev/blog/svelte-3-rethinking-reactivity)と[チュートリアル](https://svelte.dev/tutorial)を読んでください。

### Getting started
始め方

The easiest way to start building a SvelteKit app is to run `npm init`:
SvelteKitアプリの構築を始める一番簡単な方法は以下のように`npm init`を実行することです。

```bash
mkdir my-app
cd my-app
npm init svelte@next
npm install
npm run dev
```

This will scaffold a new project in the `my-app` directory, install its dependencies, and start a server on [localhost:3000](http://localhost:3000). Try editing the files to get a feel for how everything works – you may not need to bother reading the rest of this guide!
これで、`my-app`ディレクトリに新しいプロジェクトの足掛かりを作成し、依存しているものをインストールし、[localhost:3000](http://localhost:3000)でサーバーを起動します。ファイルを編集して、どのように動作しているのか感じを掴んでみてください（このガイドの残りをわざわざ読まなくてよいかもしれません）。
