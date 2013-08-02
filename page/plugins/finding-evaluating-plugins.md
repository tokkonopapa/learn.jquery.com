---
title   : プラグインを見つけて評価する
level:        intermediate
source: http://jqfundamentals.com/legacy
attribution:
  - プラグインの基礎
---

<!--
One of the most celebrated aspects of jQuery is its extensive plugin ecosystem. From table sorting to form validation to autocompletion – if there's a need for it, chances are good that someone has written a plugin for it.
-->
jQuery の最も良く知られた特徴の1つに、プラグイン拡張のエコシステムが挙げられます。テーブルのソーティングから自動補完を備えたフォームのバリデーションまで、必要とされるところには大抵、誰かが書いたプラグインがあります。

<!--
The quality of jQuery plugins varies widely. Many plugins are extensively tested and well-maintained, but others are hastily created and then ignored. More than a few fail to follow best practices. Some plugins, mainly [jQuery UI](http://jqueryui.com/), are maintained by the jQuery team. The quality of these plugins is as good as jQuery itself.
-->
それら jQuery プラグインの品質にはかなりのばらつきがあります。多くのプラグインは、広範囲にわたりテストされよくメンテナンスされていますが、そうでないものはそこそこに作られやがて忘れられていきます。残念ながらベスト・プラクティスに従っていないものも少なくはありません。あるプラグインは、主に [jQuery UI](http://jqueryui.com/) のような類いですが、jQuery チームによって維持されています。これらのプラグインの品質は jQuery そのものと同等です。

<!--
Google is your best initial resource for locating plugins, though the jQuery team is working on an improved plugin repository. Once you've identified some options via a Google search, you may want to consult the jQuery mailing list or the `#jquery` IRC channel to get input from others.
-->
Google は最初にプラグインを探すのに適した情報源ですが、jQuery チームにより改善され続けているプラグインのリポジトリがあります。一旦 Google 検索で幾つかのオプションを見つけたあとは、jQuery のメーリングリストか  IRC チャンネルの `#jquery` で他の人たちから情報を得ることができます。

<!--
When looking for a plugin to fill a need, do your homework. Ensure that the plugin is well-documented, and look for the author to provide lots of examples of its use. Be wary of plugins that do far more than you need; they can end up adding substantial overhead to your page. For more tips on spotting a sub-par plugin, read [Signs of a poorly written jQuery plugin](http://remysharp.com/2010/06/03/signs-of-a-poorly-written-jquery-plugin/) by Remy Sharp.
-->
ニーズを満たすプラグインを見つけるにはあなた自身の仕事です。よく整備されたドキュメントと、多く使用例を提供しているプラグインの作者を見つけて下さい。あなたが必要とする以上のことを行っているプラグインにも注意すべきです。それらはあなたのページに相当な負荷を追加して終わることがあります。プラグインにまつわるより詳しい情報は、Remy Sharp の [Signs of a poorly written jQuery plugin](http://remysharp.com/2010/06/03/signs-of-a-poorly-written-jquery-plugin/) を読んで下さい。

<!--
Once you choose a plugin, you'll need to add it to your page. Download the plugin, unzip it if necessary, place it your application's directory structure, then include the plugin in your page using a script tag (after you include jQuery).
-->
一旦プラグインを選んだら、それをページに加える必要があります。プラグインをダウンロードし、必要なら解凍し、アプリケーションのディレクトリ階層に配置します。そしてプラグインをページに読み込むためのスクリプトタグを（jQuery を読み込んだあとに）ページに記述します。
