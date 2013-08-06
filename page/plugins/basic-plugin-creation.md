---
title   : プラグインの基本的な作り方
level:        intermediate
source: http://jqfundamentals.com/legacy
attribution:
  - プラグインの基礎
---

<!--
Sometimes you want to make a piece of functionality available throughout your code. For example, perhaps you want a single method you can call on a jQuery selection that performs a series of operations on the selection. Maybe you wrote a really useful utility function that you want to be able to move easily to other projects. In this case, you may want to write a plugin.
-->
時には、あなたのコードの至る所で何らかの機能を追加したいと思うことがあるでしょう。例えば jQuery のセレクタで集められた要素の集合に一連の処理を実行する単一のメソッドが欲しい時などです。しかも他のプロジェクトに容易に転用できる、本当に使えるユーティリティー関数を書きたいところでしょう。このような場合には、プラグインを書くのが適切です。

<!--
## How jQuery Works 101: jQuery Object Methods and Utility Methods
-->
## jQuery の働き 101: jQuery のオブジェクト・メソッドとユーティリティー・メソッド

<!--
Before we write our own plugins, we must first understand a little about how jQuery works. Take a look at this code:
-->
独自のプラグインを書く前に、まずは jQuery がどのように働くかをちょっと知っておきましょう。次のコードをみて下さい。

```
$( "a" ).css( "color", "red" );
```

<!--
This is some pretty basic jQuery code, but do you know what's happening behind the scenes? Whenever you use the `$` function to select elements, it returns a jQuery object. This object contains all of the methods you've been using (`.css()`, `.click()`, etc.) and all of the elements that fit your selector. The jQuery object gets these methods from the `$.fn` object. This object contains all of the jQuery object methods, and if we want to write our own methods, it will need to contain those as well.
-->
これは本当に小さな jQuery のベーシックなコードですが、その裏では何が起きているか知っていますか？ `$` 関数で要素を選択すると jQuery オブジェクトが返ります。このオブジェクトには jQuery で使えるすべてのメソッド（`.css()` や `.click()` など）とセレクタに合致したすべての要素が含まれています。jQuery オブジェクトはこれらのメソッドを `$.fn` オブジェクトから得ています。これには jQuery オブジェクトのメソッドすべてが含まれています。そして独自のメソッドを書く場合にも、これらを同様に含む必要があるのです。

<!--
Additionally the jQuery utility method `$.trim()` is used above to remove any leading or trailing empty space characters from the user input. Utility methods are functions that reside directly in the `$` function itself. You may occasionally want to write a utility method plugin when your extension to the jQuery API does not have to do something to a set of DOM elements you've retrieved.
-->
加えて上の例では、ユーザーの入力から先頭や末尾の空白文字を取り除く jQuery のユーティリティー・メソッド `$.trim()` が使われています。ユーティリティー・メソッドは、`$` 自身に直接属する関数です。拡張した jQuery API で取得した DOM 要素の集合に何らかを行う以外にも、時にはこうしたユーティリティー・メソッドのプラグインを書きたいこともあるでしょう。

<!--
## Basic Plugin Authoring
-->
## プラグインの基本的な書き方

<!--
Let's say we want to create a plugin that makes text within a set of retrieved elements green. All we have to do is add a function called `greenify` to `$.fn` and it will be available just like any other jQuery object method.
-->
ここで、取得した要素の集合にあるテキストを緑に変えるプラグインを作ってみましょう。すべきことは、`$.fn` に `greenify` という関数を加えることです。そうすれば他の jQuery オブジェクトのメソッドと同様に使えるようになります。

<!--
```
$.fn.greenify = function() {
	this.css( "color", "green" );
};

$( "a" ).greenify(); // Makes all the links green.
```
-->
```
$.fn.greenify = function() {
	this.css( "color", "green" );
};

$( "a" ).greenify(); // すべてのリンクを緑に変えます
```

<!--
Notice that to use `.css()`, another method, we use `this`, not `$( this )`. This is because our `greenify` function is a part of the same object as `.css()`.
-->
ここで `.css()` を使うためのもう1つのメソッドには `$( this )` ではなく `this` を使っていることに注意して下さい。これは `greenify` 関数が `.css()` と同じオブジェクトの一部（訳者中：即ち `this` は、jQuery オブジェクト自体を指す）だからなのです。

<!--
## Chaining
-->
## チェーン

<!--
This works, but there's a couple of things we need to do for our plugin to survive in the real world. One of jQuery's features is chaining, when you link five or six actions onto one selector. This is accomplished by having all jQuery object methods return the original jQuery object again (there are a few exceptions: `.width()` called without parameters returns the width of the selected element, and is not chainable). Making our plugin method chainable takes one line of code:
-->
このコードは動作はしますが、実際に使えるようにするためには幾つか手を加えるべき点があります。jQuery の特徴の1つにチェーンがあります。これにより、1つのセレクタに5つか6つのメソッドをつなげられるのです。これは、すべての jQuery オブジェクトのメソッドが元の jQuery オブジェクトをリターンすることにより実現されます（幾つかの例外があります：例えば引数なしで呼び出す `.width()` は選択された要素の幅を返すだけなので、チェーン出来ません）。そこでプラグインに1行加えてチェーンを可能にしましょう。

```
$.fn.greenify = function() {
	this.css( "color", "green" );
	return this;
}

$( "a" ).greenify().addClass( "greenified" );
```

<!--
Note that the notion of chaining is *not* applicable to jQuery utility methods like `$.trim()`.
-->
チェーンは、`$.trim()` のような jQuery のユーティリティー・メソッドには適用 *できない* ことに注意しましょう。

<!--
## Protecting the $ Alias and Adding Scope
-->
## $ エイリアスを保護し、スコープを追加する

<!--
The `$` variable is very popular among JavaScript libraries, and if you're using another library with jQuery, you will have to make jQuery not use the `$` with `jQuery.noConflict()`. However, this will break our plugin since it is written with the assumption that `$` is an alias to the `jQuery` function. To work well with other plugins, _and_ still use the jQuery `$` alias, we need to put all of our code inside of an [Immediately Invoked Function Expression](http://stage.learn.jquery.com/javascript-101/functions/#immediately-invoked-function-expression), and then pass the function `jQuery`, and name the parameter `$`:
-->
変数 `$` は JavaScript ライブラリーの間では非常にポピュラーなので、jQuery と他のライブラリーと一緒に用いる場合、`jQuery.noConflict()` で jQuery が `$` を使わないようにしなければなりません。しかしそれでは、`$` が `jQuery` 関数のエイリアスであるという想定で書かれたプラグインが動作しなくなってしまいます。他のプラグインとも共存でき、かつ jQuery の `$` エイリアスが使えるようにするには、[即時関数](http://stage.learn.jquery.com/javascript-101/functions/#immediately-invoked-function-expression) の内部にすべてのコードを記述し、関数 `jQuery` を `$` として引き渡します。

```
(function ( $ ) {

	$.fn.greenify = function() {
		this.css( "color", "green" );
		return this;
	};

	$.ltrim = function( str ) {
		return str.replace( /^\s+/, "" );
	};

	$.rtrim = function( str ) {
		return str.replace( /\s+$/, "" );
	};

}( jQuery ));
```

<!--
In addition, the primary purpose of an Immediately Invoked Function is to allow us to have our own private variables. Pretend we want a different color green, and we want to store it in a variable.
-->
加えて言うなら、即時関数の第1の目的はプライベートな独自の変数を使えるようにすることにあります。緑とは違う色にして、変数に入れることにしましょう。

```
(function ( $ ) {

	var shade = "#556b2f";

	$.fn.greenify = function() {
		this.css( "color", shade );
		return this;
	};

}( jQuery ));
```

<!--
## Minimizing Plugin Footprint
-->
## プラグインの占有領域を最小にする

<!--
It's good practice when writing plugins to only take up one slot within `$.fn`. This reduces both the chance that your plugin will be overridden, and the chance that your plugin will override other plugins. In other words, this is bad:
-->
`$.fn` 中の1スロットに閉じ込めたプラグインの書き方は、良い書き方です。そうしておけば、作ったプラグインが書き換えられたり、既存のプラグインを書き換えたりすることを防ぐことができます。言い換えれば、次の例は悪い書き方です。

```
(function( $ ) {

	$.fn.openPopup = function() {
		// Open popup code.
	};

	$.fn.closePopup = function() {
		// Close popup code.
	};

}( jQuery ));
```

<!--
It would be much better to have one slot, and use parameters to control what action that one slot performs.
-->
次のように1つのスロットにして、それを制御するために引数を使う方が良い書き方です。

```
(function( $ ) {

	$.fn.popup = function( action ) {

		if ( action === "open") {
			// Open popup code.
		}

		if ( action === "close" ) {
			// Close popup code.
		}

	};

}( jQuery ));
```

<!--
## Using the `each()` Method
-->
## `each()` メソッドの使用

<!--
Your typical jQuery object will contain references to any number of DOM elements, and that's why jQuery objects are often referred to as collections. If you want to do any manipulating with specific elements (e.g. getting a data attribute, calculating specific positions) then you need to use `.each()` to loop through the elements.
-->
jQuery オブジェクトは通常、幾つかの DOM 要素への参照を含むので、しばしば jQuery オブジェクトは集合への参照と見なされます。それ故、特定の要素を操作しようとする時（例えばデータ属性を取得したり、位置を算出するなどの場合）には、それら要素に `each()` のループを用いる必要があります。

<!--
```
$.fn.myNewPlugin = function() {

	return this.each(function() {
		// Do something to each element here.
	});

};
```
-->
```
$.fn.myNewPlugin = function() {

	return this.each(function() {
		// ここで各要素に何かする
	});

};
```

<!--
Notice that we return the results of `.each()` instead of returning `this`. Since `.each()` is already chainable, it returns `this`, which we then return. This is a better way to maintain chainability than what we've been doing so far.
-->
ここでは `this` ではなく `.each()` の結果をリターンすることに注意しましょう。なぜなら `.each()` は既にチェーン可能な `this` を返すからです。これは、チェーン可能なオブジェクトを維持するための（今までみてきた例よりも）より良い方法です。

<!--
## Accepting Options
-->
## オプションの受け取り

<!--
As your plugins get more and more complex, it's a good idea to make your plugin customizable by accepting options. The easiest way do this, especially if there are lots of options, is with an object literal. Let's change our greenify plugin to accept some options.
-->
プラグインがさらに複雑になってくると、オプションを受け取ってカスタマイズできるようしておくとよいでしょう。このための最も簡単な方法として、特にオプションが多数ある場合には、オブジェクト・リテラルを使います。先の greenify プラグインが、幾つかのオプションを受け取れるようにしてみましょう。

<!--
```
(function ( $ ) {

	$.fn.greenify = function( options ) {

		// This is the easiest way to have default options.
		var settings = $.extend({
			// These are the defaults.
			color: "#556b2f",
			backgroundColor: "white"
		}, options );

		// Greenify the collection based on the settings variable.
		return this.css({
			color: settings.color,
			backgroundColor: settings.backgroundColor
		});

	};

}( jQuery ));
```
-->
```
(function ( $ ) {

	$.fn.greenify = function( options ) {

		// デフォルトのオプションを持つ最も簡単な方法
		var settings = $.extend({
			// These are the defaults.
			color: "#556b2f",
			backgroundColor: "white"
		}, options );

		// 設定された値に従って集合に色をつけます
		return this.css({
			color: settings.color,
			backgroundColor: settings.backgroundColor
		});

	};

}( jQuery ));
```

<!--
Example usage:
-->
使用例：

```
$( "div" ).greenify({
	color: "orange"
});
```

<!--
The default value for `color` of `#556b2f` gets overridden by `$.extend()` to be orange.
-->
`color` のデフォルト値 `#556b2f` は `$.extend()` で上書きされ、オレンジになります。

<!--
## Putting It Together
-->
## まとめ

<!--
Here's an example of a small plugin using some of the techniques we've discussed:
-->
これまでのテクニックを使った小さなプラグインの例をここに示します。

```
(function( $ ) {

	$.fn.showLinkLocation = function() {

		return this.filter( "a" ).each(function() {
			$( this ).append( " (" + $( this ).attr( "href" ) + ")" );
		});

	};

}( jQuery ));

 // Usage example:
 $( "a" ).showLinkLocation();
```

<!--
This handy plugin goes through all anchors in the collection and appends the `href` attribute in brackets.
-->
この軽量なプラグインは、集合中のすべてのアンカーに作用し、カッコで括られた `href` 属性を追加します。

<!--
```
<!-- Before plugin is called: -- >
<a href="page.html">Foo</a>

<!-- After plugin is called: -- >
<a href="page.html">Foo (page.html)</a>
```
-->
```
<!-- プラグイン呼び出し前 -->
<a href="page.html">Foo</a>

<!-- プラグイン呼び出し後 -->
<a href="page.html">Foo (page.html)</a>
```

<!--
Our plugin can be optimized though:
-->
このプラグインは、次のように最適化することができます：

```
(function( $ ) {

	$.fn.showLinkLocation = function() {

		return this.filter( "a" ).append(function() {
			return " (" + this.href + ")";
		});

	};

}( jQuery ));
```

<!--
We're using the `.append()` method's capability to accept a callback, and the return value of that callback will determine what is appended to each element in the collection. Notice also that we're not using the `.attr()` method to retrieve the `href` attribute, because the native DOM API gives us easy access with the aptly named `href` property.
-->
ここでは `.append()` メソッドがその引数にコールバックを受け取れることを利用し、そのコールバックの戻り値が集合中の各要素に追加すべき内容を決めています。ここではまた、`href` 属性の取得に `attr()` メソッドを使っていなことに注目しましょう。ネイティブな DOM API により `href` 属性に簡単にアクセスするできるからです。
