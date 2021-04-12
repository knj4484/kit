---
title: AMP
---

An unfortunate reality of modern web development is that it is sometimes necessary to create an [AMP](https://amp.dev/) version of your site. In SvelteKit this can be done by setting the [`amp`](#configuration-amp) config option, which has the following effects:
モダンなWeb開発の不幸な現実として、サイトの[AMP](https://amp.dev/)バージョンを作成しなければならないことがあります。SvelteKitでは、設定オプションの[`amp`](#configuration-amp)をセットすることでAMPバージョンの作成ができます。このオプションには以下の効果があります。

* Client-side JavaScript, including the router, is disabled
* ルーターを含むクライアントサイドJavaScriptが無効になります
* Styles are concatenated into `<style amp-custom>`, and the [AMP boilerplate](https://amp.dev/boilerplate/) is injected
* スタイルは`<style amp-custom>`の中に連結され、[AMP boilerplate](https://amp.dev/boilerplate/)が差し込まれます
* In development, requests are checked against the [AMP validator](https://validator.ampproject.org/) so you get early warning of any errors
* 開発時に、リクエストが[AMP validator](https://validator.ampproject.org/)に対してチェックされ、エラーが何かあれば早めに警告を得られます