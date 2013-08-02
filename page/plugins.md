---
title: Plugins
level: intermediate
customFields:
 -
    key: "icon"
    value: "bolt"
---

<!--
A jQuery plugin is simply a new method that we use to extend jQuery's prototype object. By extending the prototype object you enable all jQuery objects to inherit any methods that you add. As established, whenever you call `jQuery()` you're creating a new jQuery object, with all of jQuery's methods inherited.
-->
jQuery プラグインは、jQuery のプロトタイプ・オブジェクトを拡張するためのシンプルな新しい手法です。プロトタイプ・オブジェクトを拡張することにより、追加した任意のメソッドですべての jQuery オブジェクトを継承することが出来ます。`jQuery()` を呼び出す度に、すべての jQuery メソッドを継承した新しい jQuery オブジェクトを作ることになるのです。

<!--
The idea of a plugin is to do something with a collection of elements. You could consider each method that comes with the jQuery core a plugin, like `.fadeOut()` or `.addClass()`.
-->
要素の集合を使って何かをすることが、プラグインの基本的な着想です。その中では `.fadeOut()` や `.addClass()` といった jQuery コア・プラグインのメソッドの利用が可能です。

<!--
You can make your own plugins and use them privately in your code or you can release them into the wild. There are thousands of jQuery plugins available online. The barrier to creating a plugin of your own is so low that you'll want to do it straight away!
-->
あなたは、自分のコードで使うプライベートなプラグインを作ったり、世間に向けてリリースすることが出来ます。オンライン上には、何千という jQuery プラグインがあります。独自のプラグインを作ることは、すぐにでもそうしたいと思うほどに簡単なことなのです！
