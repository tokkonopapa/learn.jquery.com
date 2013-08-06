---
title: 高度なプラグインの概念
level: intermediate
---

<!--
### Provide Public Access to Default Plugin Settings
-->
### プラグインのデフォルト設定へアクセスする方法を公開する

<!--
An improvement we can, and should, make to the code above is to expose the default plugin settings. This is important because it makes it very easy for plugin users to override/customize the plugin with minimal code. And this is where we begin to take advantage of the function object.
-->
前章のコードにできる、そしてすべき改善は、プラグインのデフォルト設定を公開するコードにすることです。プラグインのユーザーがそれらを上書きし、最小限のコードでプラグインのカスタマイズを容易にする上で、これは重要なことです。そこで、まずは関数オブジェクトの活用から始めましょう。

<!--
```
// Plugin definition.
$.fn.hilight = function( options ) {

	// Extend our default options with those provided.
	// Note that the first argument to extend is an empty
	// object – this is to keep from overriding our "defaults" object.
	var opts = $.extend( {}, $.fn.hilight.defaults, options );

	// Our plugin implementation code goes here.

};

// Plugin defaults – added as a property on our plugin function.
$.fn.hilight.defaults = {
	foreground: "red",
	background: "yellow"
};
```
-->
```
// プラグインの定義
$.fn.hilight = function( options ) {

	// 受け取った値でプラグインのデフォルト・オプションを拡張する
	// extend への最初の引数が空オブジェクトであることに注意
	// これにより、"defaults" オブジェクトを上書きしてしまうことを防ぐ
	var opts = $.extend( {}, $.fn.hilight.defaults, options );

	// ここにプラグインを実装するコードを記述

};

// プラグインのデフォルト値 – プラグイン関数のプロパティに defaults を追加する
$.fn.hilight.defaults = {
	foreground: "red",
	background: "yellow"
};
```

<!--
Now users can include a line like this in their scripts:
-->
これによってユーザーは、スクリプトに次の様な1行を含めることができるようになります。

<!--
```
// This needs only be called once and does not
// have to be called from within a "ready" block
$.fn.hilight.defaults.foreground = "blue";
```
-->

```
// 次の行は1度だけ呼び出せば十分で、"ready"
// ブロックの中から呼び出す必要はない
$.fn.hilight.defaults.foreground = "blue";
```

<!--
And now we can call the plugin method like this and it will use a blue foreground color:
-->
そして次のようにプラグインのメソッドを呼び出せば、青いフォアグラウンド色が使われるようになります。

```
$( "#myDiv" ).hilight();
```

<!--
As you can see, we've allowed the user to write a single line of code to alter the default foreground color of the plugin. And users can still selectively override this new default value when they want:
-->
お分かりの通り、ユーザーが1行のコードを追加することで、プラグイン・デフォルトのフォアグラウンド色を変えることができるのです。しかもこの新しいデフォルト値は何時でも上書きすることができます。

<!--
```
// Override plugin default foreground color.
$.fn.hilight.defaults.foreground = "blue";

// ...

// Invoke plugin using new defaults.
$( ".hilightDiv" ).hilight();

// ...

// Override default by passing options to plugin method.
$( "#green" ).hilight({
	foreground: "green"
});
```
-->

```
// プラグイン・デフォルトのフォアグラウンド色を上書きする
$.fn.hilight.defaults.foreground = "blue";

// ...

// プラグインの新しいデフォルト値で実行する
$( ".hilightDiv" ).hilight();

// ...

// プラグインのメソッドにオプションを引き渡し、デフォルト値を上書きする
$( "#green" ).hilight({
	foreground: "green"
});
```

<!--
### Provide Public Access to Secondary Functions as Applicable
-->
### 補助的な関数を公開しアクセスできるようにする

<!--
This item goes hand-in-hand with the previous item and is an interesting way to extend your plugin (and to let others extend your plugin). For example, the implementation of our plugin may define a function called "format" which formats the hilight text. Our plugin may now look like this, with the default implementation of the format method defined below the hilight function:
-->
この章は前章と同様、プラグインを拡張する（他の人に拡張してもらう）ための興味深い方法です。例えば、ハイライトするテキストを整形する "format" という関数の定義をプラグインが実装しているとします。次のように、ハイライト関数に続き、デフォルトの format メソッドの定義を実装することになるでしょう。

<!--
```
// Plugin definition.
$.fn.hilight = function( options ) {

	// Iterate and reformat each matched element.
	return this.each(function() {

		var $this = $( this );

		// ...

		var markup = $this.html();

		// Call our format function.
		markup = $.fn.hilight.format( markup );

		$this.html( markup );

	});

};

// Define our format function.
$.fn.hilight.format = function( txt ) {
	return "<strong>" + txt + "</strong>";
};
```
-->
```
// プラグインの定義
$.fn.hilight = function( options ) {

	// マッチした要素それぞれに対して書式化する
	return this.each(function() {

		var $this = $( this );

		// ...

		var markup = $this.html();

		// 書式化関数を呼び出す
		markup = $.fn.hilight.format( markup );

		$this.html( markup );

	});

};

// デフォルトの書式化関数を定義する
$.fn.hilight.format = function( txt ) {
	return "<strong>" + txt + "</strong>";
};
```

<!--
We could have just as easily supported another property on the options object that allowed a callback function to be provided to override the default formatting. That's another excellent way to support customization of your plugin. The technique shown here takes this a step further by actually exposing the format function so that it can be redefined. With this technique it would be possible for others to ship their own custom overrides of your plugin – in other words, it means others can write plugins for your plugin.
-->
これで簡単に、オプション・オブジェクトのプロパティ以外にもう1つ、コールバック関数でデフォルトの書式化をオーバーライドできるプロパティをサポートすることができました。これこそプラグインのカスタマイズをサポートするもう1つの優れた方法なのです。ここに示すテクニックは、再定義できるよう書式化関数を公開するという、さらに一歩進んだ手法です。このテクニックにより、他の人があなたのプラグインを独自にオーバーライドしてリリースすることができるようになります。言い換えれば、あなたのプラグイン用のプラグインを他の誰かが書けるということです。

<!--
Considering the trivial example plugin we're building in this article, you may be wondering when this would ever be useful. One real-world example is the [Cycle Plugin](http://malsup.com/jquery/cycle/). The Cycle Plugin is a slideshow plugin which supports a number of built-in transition effects – scroll, slide, fade, etc. But realistically, there is no way to define every single type of effect that one might wish to apply to a slide transition. And that's where this type of extensibility is useful. The Cycle Plugin exposes a "transitions" object to which users can add their own custom transition definitions. It's defined in the plugin like this:
-->
この記事に出てくる取るに足らないプラグインの例だけでは、どのぐらい有用なのだろうと疑問に思うかもしれません。1つの現実的な例として、[Cycle Plugin](http://malsup.com/jquery/cycle/) を挙げます。このプラグインは、多数のビルトインされた切り替え効果、例えばスクロール、スライド、フェードなどを備えたスライドショーのプラグインです。しかし現実的には、誰かが望むかもしれないあらゆる切り替え効果を定義する方法はありません。そういう場合にこそ、この手の拡張が役に立つのです。Cycle Plugin は "transitions" オブジェクトを公開することで、ユーザーが独自に切り替え効果の定義を追加することができるのです。このプラグインの定義は次のようになるでしょう。

```
$.fn.cycle.transitions = {

	// ...

};
```

<!--
This technique makes it possible for others to define and ship transition definitions that plug-in to the Cycle Plugin.
-->
このテクニックにより、Cycle Plugin のための切り替え効果を定義したプラグインをリリースすることができるのです。

<!--
### Keep Private Functions Private
-->
### プライベートな関数はあくまでプライベートに

<!--
The technique of exposing part of your plugin to be overridden can be very powerful. But you need to think carefully about what parts of your implementation to expose. Once it's exposed, you need to keep in mind that any changes to the calling arguments or semantics may break backward compatibility. As a general rule, if you're not sure whether to expose a particular function, then you probably shouldn't.
-->
あなたのプラグインの一部を公開しオーバーライドさせるテクニックは非常に強力です。しかしどの実装部分を公開するかは慎重に考えなければなりません。一旦公開をすると、呼び出しの引数や意味を変更する際、後方互換が失われる可能性のあることを常に心に留めておく必要があります。一般論として、ある関数を公開するかどうか迷ったら、おそらく止めておくべきでしょう。

<!--
So how then do we define more functions without cluttering the namespace and without exposing the implementation? This is a job for closures. To demonstrate, we'll add another function to our plugin called "debug". The debug function will log the number of selected elements to the Firebug console. To create a closure, we wrap the entire plugin definition in a function (as detailed in the jQuery Authoring Guidelines).
-->
それでは名前空間を汚すことなく、そして実装を公開することなく、更なる関数を定義するにはどうしたら良いでしょうか？ これはクロージャーの仕事です。例えば Firebug のコンソールに選択された要素の数をログするデバック関数を考えてみましょう。クロージャーを作るには、（jQuery のオーサリング・ガイドラインに詳しくある通り）関数の定義全体を function で囲います。

<!--
```
// Create closure.
(function( $ ) {

	// Plugin definition.
	$.fn.hilight = function( options ) {
		debug( this );
		// ...
	};

	// Private function for debugging.
	function debug( $obj ) {
		if ( window.console && window.console.log ) {
			window.console.log( "hilight selection count: " + $obj.size() );
		}
	};

	// ...

// End of closure.

})( jQuery );
```
-->
```
// クロージャーを作成する
(function( $ ) {

	// プラグインの定義
	$.fn.hilight = function( options ) {
		debug( this );
		// ...
	};

	// デバッグ用のプライベートな関数
	function debug( $obj ) {
		if ( window.console && window.console.log ) {
			window.console.log( "hilight selection count: " + $obj.size() );
		}
	};

	// ...

// クロージャーの終了

})( jQuery );
```

<!--
Our "debug" method cannot be accessed from outside of the closure and thus is private to our implementation.
-->
"debug" メソッドはクロージャーの外からはアクセスできず、プライベートな関数として実装する事ができます。

<!--
### Support the Metadata Plugin
-->
### メタ・データをサポートするプラグイン

<!--
Depending on the type of plugin you're writing, adding support for the [Metadata Plugin](http://docs.jquery.com/Plugins/Metadata/metadata) can make it even more powerful. Personally, I love the Metadata Plugin because it lets you use unobtrusive markup to override plugin options (which is particularly useful when creating demos and examples). And supporting it is very simple!
-->
プラグインの種類によっては、[メタデータ・プラグイン](http://docs.jquery.com/Plugins/Metadata/metadata) がさらに強力な力を発揮します。個人的にはメタデータ・プラグインが好みです。なぜなら、プラグインのオプションをオーバーライドするのにとても洗練されたマークアップが可能となるからです（とりわけデモとか例題を作成する時に役に立ちます）。そしてそのサポートは非常に簡単なのです！

<!--
```
// Plugin definition.
$.fn.hilight = function( options ) {

	// Build main options before element iteration.
	var opts = $.extend( {}, $.fn.hilight.defaults, options );

	return this.each(function() {
		var $this = $( this );

		// Build element specific options.
		// This changed line tests to see if the Metadata Plugin is installed,
		// And if it is, it extends our options object with the extracted metadata.
		var o = $.meta ? $.extend( {}, opts, $this.data() ) : opts;

		//...

	});

};
```
-->
```
// プラグインの定義
$.fn.hilight = function( options ) {

	// 要素の繰り返しの前にメインのオプションを構築する
	var opts = $.extend( {}, $.fn.hilight.defaults, options );

	return this.each(function() {
		var $this = $( this );

		// 要素に応じたオプションを構築する
		// メタデータ・プラグインがインストールされているかをテストします。
		// もしあるなら、抽出されたメタデータを使ってオプションを拡張します。
		var o = $.meta ? $.extend( {}, opts, $this.data() ) : opts;

		//...

	});

};
```

<!--
*Note:* This line is added as the last argument to *jQuery.extend* so it will override any other option settings. Now we can drive behavior from the markup if we choose:
-->
*注意：* この行は他のオプション設定をオーバーライドするよう *jQuery.extend* の最後に追加します。次のようにマークアップすれば、振る舞いを変えることができます。

```
<!--  markup  -->
<div class="hilight { background: 'red', foreground: 'white' }">
	Have a nice day!
</div>
<div class="hilight { foreground: 'orange' }">
	Have a nice day!
</div>
<div class="hilight { background: 'green' }">
	Have a nice day!
</div>
```

<!--
And now we can hilight each of these `<div>`s uniquely using a single line of script:
-->
たった1行のスクリプトで、それぞれの `<div>` ごとにユニークにハイライトする事ができます。

```
$( ".hilight" ).hilight();
```

<!--
###Bob and Sue
-->
### ボブとスー

<!--
Let's say Bob has created a wicked new gallery plugin (called "superGallery") which takes a list of images and makes them navigable. Bob's thrown in some animation to make it more interesting. He's tried to make the plugin as customizable as possible, and has ended up with something like this:
-->
ボブがすごいギャラリー・プラグイン（"superGallery" と呼びます）を作ったとしましょう。このプラグインは画像のリストを受け取り、それらをナビゲーション出来るようにします。ボブのプラグインはより興味よ引くよう幾つかのアニメーションを実行します。彼はできる限りカスタマイズが可能なプラグインを作ろうと、次のように実装しました：

```
jQuery.fn.superGallery = function( options ) {

	// Bob's default settings:
	var defaults = {
		textColor: "#000",
		backgroundColor: "#fff",
		fontSize: "1em",
		delay: "quite long",
		getTextFromTitle: true,
		getTextFromRel: false,
		getTextFromAlt: false,
		animateWidth: true,
		animateOpacity: true,
		animateHeight: true,
		animationDuration: 500,
		clickImgToGoToNext: true,
		clickImgToGoToLast: false,
		nextButtonText: "next",
		previousButtonText: "previous",
		nextButtonTextColor: "red",
		previousButtonTextColor: "red"
	};

	var settings = $.extend( {}, defaults, options );

	return this.each(function() {
		// Plugin code would go here...
	});

};
```

<!--
The first thing that probably comes to your mind (OK, maybe not the first) is the prospect of how huge this plugin must be to accommodate such a level of customization. The plugin, if it weren't fictional, would probably be a lot larger than necessary. There are only so many kilobytes people will be willing to spend!
-->
このコードをみて最初に心に浮かぶのは（別に最初じゃなくても構わないですが）、このプラグインがあらゆるレベルのカスタマイズに応えるために、どれだけ多くの便宜を図る必要があるかという予想ではないでしょうか。実際にこのプラグインは、必要以上に肥大するでしょう。人々が使ってみたいと思うのはせいぜい数キロバイト程度なのです。

<!--
Now, our friend Bob thinks this is all fine; in fact, he's quite impressed with the plugin and its level of customization. He believes that all the options make for a more versatile solution, one which can be used in many different situations.
-->
さて、我らが友人のボブはこれで良しとし、プラグインとそのカスタマイズのレベルにかなり満足しています。すべてのオプションはより汎用性のあるソリューションのためであり、それぞれがより多くの異なる状況で使えると信じています。

<!--
Sue, another friend of ours, has decided to use this new plugin. She has set up all of the options required and now has a working solution sitting in front of her. It's only five minutes later, after playing with the plugin, that she realizes the gallery would look much nicer if each image's width were animated at a slower speed. She hastily searches through Bob's documentation but finds no *animateWidthDuration* option!
-->
さてもう1人の友人スーは、この新しいプラグインを使おうと思い立ちました。彼女は必要とされるオプションをすべて設定し、動作するソリューションを得ました。プラグインを5分間いじった後に彼女は考えました。それぞれの画像の幅がもっとゆっくりアニメーションすればずっと素敵なのに、と。そこで彼女はボブのドキュメントを探しましたが、*animateWidthDuration* というオプションは見つかりません！

<!--
### Do You See The Problem?
-->
### 何が問題なのでしょう？

<!--
It's not really about how many options your plugin has; but what options it has!
-->
このプラグインがどれだけたくさんのオプションを持っているかは問題ではありません。何をオプションに持つかなのです！

<!--
Bob has gone a little over the top. The level of customization he's offering, while it may seem high, is actually quite low, especially considering all the possible things one might want to control when using this plugin. Bob has made the mistake of offering a lot of ridiculously specific options, rendering his plugin much more difficult to customize!
-->
ボブは少々行き過ぎでした。彼が提供したカスタマイズのレベルは一見高そうに見えますが、実は極めて低いものなのです。特にこのプラグインを使う時に制御したいと思うことを、考えうる限りの可能性をもって考慮した点です。ボブは特定のオプションをばかばかしいほど多く提供するというミスを犯したのです。その結果、彼のプラグインをカスタマイズすることが非常に困難になってしまったのです！

<!--
### A Better Model
-->
### より良いモデル

<!--
So it's pretty obvious: Bob needs a new customization model, one which does not relinquish control or abstract away the necessary details.
-->
ボブには新しいカスタマイズのモデルが必要なことは明らかです。その1つが、制御性を失わずに必要な詳細を抽象化することです。

<!--
The reason Bob is so drawn to this high-level simplicity is that the jQuery framework very much lends itself to this mindset. Offering a *previousButtonTextColor* option is nice and simple, but let's face it, the vast majority of plugin users are going to want way more control!
-->
ボブに高いレベルの簡略化が必要な理由は、jQuery のフレームワークはとてもこの考え方に適しているということです。オプション *previousButtonTextColor* を提供することは至ってシンプルですが、問題は、プラグインのユーザーの大半がより多くの制御を必要としているということなのです！

<!--
Here are a few tips which should help you create a better set of customizable options for your plugins:
-->
ここからは、よりよいカスタマイズのオプションを与えるプラグインの作り方ヒントを幾つか紹介します。

<!--
### Don't Create Plugin-specific Syntax
-->
### プラグインに特有のシンタックスを作らない

<!--
Developers who use your plugin shouldn't have to learn a new language or terminology just to get the job done.
-->
プラグインを使う開発者はただ仕事を完遂できればいいのであって、新しい言語や用語を学ぶ必要は全くありません。

<!--
Bob thought he was offering maximum customization with his *delay* option (look above). He made it so that with his plugin you can specify four different delays, "quite short," "very short," "quite long," or "very long":
-->
ボブは *delay* オプションに最大限のカスタマイズ性を与えようと考えました。そこで彼は "quite short"、"very short"、"quite long"、"very long" という4つの異なる特別な遅延を定義しました。

```
var delayDuration = 0;

switch ( settings.delay ) {

	case "very short":
		delayDuration = 100;
		break;

	case "quite short":
		delayDuration = 200;
		break;

	case "quite long":
		delayDuration = 300;
		break;

	case "very long":
		delayDuration = 400;
		break;

	default:
		delayDuration = 200;

}
```

<!--
Not only does this limit the level of control people have, but it takes up quite a bit of space. Twelve lines of code just to define the delay time is a bit much, don't you think? A better way to construct this option would be to let plugin users specify the amount of time (in milliseconds) as a number, so that no processing of the option needs to take place.
-->
これは制御の幅を狭めるだけでなく、多くのスペースを必要とします。遅延時間を定義するのに12行のコードは多過ぎるとは思いませんか？ このオプションを設定するには遅延時間の合計を（ミリ秒の）数値で指定する方がいいでしょう。そうすればこのオプションを処理するのにスペースは必要ありません。

<!--
The key here is not to diminish the level of control through your abstraction. Your abstraction, whatever it is, can be as simplistic as you want, but make sure that people who use your plugin will still have that much-sought-after low-level control! (By low-level I mean non-abstracted.)
-->
ここでのキーポイントは、抽象化によって制御のレベルを減らすことではありません。抽象化する時には、それが何であれ、思い切った単純化が必要です。それでもこのプラグインを使おうとする人にとっては、低レベルの制御（この場合の低レベルとは、抽象化していないことを指します）に勝るとも劣らない制御性を保っているのです！

<!--
### Give Full Control of Elements
-->
### 要素にすべての制御性を与える

<!--
If your plugin creates elements to be used within the DOM, then it's a good idea to offer plugin users some way to access those elements. Sometimes this means giving certain elements IDs or classes. But note that your plugin shouldn't rely on these hooks internally:
-->
もしあなたのプラグインが DOM に何かしらの要素を作成するなら、それらの要素にアクセスする手段を与えるのが良いでしょう。それは時には特定の要素への ID であったりクラスであったりします。ただし、プラグインの内部でそれらを当てにすべきではありません。

<!--
A bad implementation:
-->
悪い実装例：

<!--
```
// Plugin code
$( "<div id=\"the-gallery-wrapper\" />").appendTo( "body" );

$( "#the-gallery-wrapper" ).append( "..." );

// Retain an internal reference:
var $wrapper = $( "<div />" )
	.attr( settings.wrapperAttrs )
	.appendTo( settings.container );

$wrapper.append( "..." ); // Easy to reference later...
```
-->
```
// プラグインのコード
$( "<div id=\"the-gallery-wrapper\" />").appendTo( "body" );

$( "#the-gallery-wrapper" ).append( "..." );

// 内部に参照用の変数を保持
var $wrapper = $( "<div />" )
	.attr( settings.wrapperAttrs )
	.appendTo( settings.container );

$wrapper.append( "..." ); // あとでアクセスし易くする...
```

<!--
Notice that we've created a reference to the injected wrapper and we're also calling the ´.attr()` method to add any specified attributes to the element. So, in our settings it might be handled like this:
-->
挿入したラッパーへの参照を作成し、`.attr()` メソッドを呼び出して要素に特定の属性を追加しようとしています。この場合、次のように扱うべきでしょう：

<!--
```
var defaults = {
	wrapperAttrs : {
		id: "gallery-wrapper"
	},
	// ... rest of settings ...
};

// We can use the extend method to merge options/settings as usual:
// But with the added first parameter of TRUE to signify a DEEP COPY:
var settings = $.extend( true, {}, defaults, options );
```
-->
```
var defaults = {
	wrapperAttrs : {
		id: "gallery-wrapper"
	},
	// ... 残りの設定 ...
};

// これまで通り、オプション設定をマージするために extend メソッドを使います。
// ただし明示的に深いコピーとするために、最初のパラメータに true を与えます。
var settings = $.extend( true, {}, defaults, options );
```

<!--
The *$.extend()* method will now recurse through all nested objects to give us a merged version of both the defaults and the passed options, giving the passed options precedence.
-->
*$.extend()* メソッドは、先の例のように引数を指定すれば、階層化されたオブジェクトすべてを再帰的に辿り、`default` と先に渡されたオプションの両方をマージしたバージョンを作ってくれます。

<!--
The plugin user now has the power to specify any attribute of that wrapper element so if they require that there be a hook for any CSS styles then they can quite easily add a class or change the name of the ID without having to go digging around in plugin source.
-->
これによりプラグインのユーザーは、ラッパー要素に CSS のフックとなる任意の属性を与えられるようになり、プラグインのソースを深いところまで読み込むことなく、クラスを追加したり ID 名を変更したりすることが容易にのです。

<!--
The same model can be used to let the user define CSS styles:
-->
これと同じモデルで、ユーザーが定義する CSS を使えるようにする例です：

<!--
```
var defaults = {
	wrapperCSS: {},
	// ... rest of settings ...
};

// Later on in the plugin where we define the wrapper:
var $wrapper = $( "<div />" )
	.attr( settings.wrapperAttrs )
	.css( settings.wrapperCSS ) // ** Set CSS!
	.appendTo( settings.container );
```
-->
```
var defaults = {
	wrapperCSS: {},
	// ... 残りの設定 ...
};

// プラグインの後半で、ラッパーを定義する
var $wrapper = $( "<div />" )
	.attr( settings.wrapperAttrs )
	.css( settings.wrapperCSS ) // ** CSS を設定!
	.appendTo( settings.container );
```

<!--
Your plugin may have an associated stylesheet where developers can add CSS styles. Even in this situation it's a good idea to offer some convenient way of setting styles in JavaScript, without having to use a selector to get at the elements.
-->
あなたのプラグインには関連するスタイルシートがあり、開発者が CSS を追加できるのかもしれません。その場合でも、要素を得るためにセレクタを使わせることなく、JavaScript でスタイルを設定するのに便利な方法を提供することは良いことなのです。

<!--
### Provide Callback Capabilities
-->
### コールバック機能を提供する

<!--
*What is a callback?* – A callback is essentially a function to be called later, normally triggered by an event. It's passed as an argument, usually to the initiating call of a component, in this case, a jQuery plugin.
-->
*コールバックとは何でしょう？* – 基本的には、何かしらのイベントによって後で呼び出されれる関数のことです。通常は、コンポーネントを起動する関数（この場合は jQuery プラグイン）に引数として引き渡されます。

<!--
If your plugin is driven by events then it might be a good idea to provide a callback capability for each event. Plus, you can create your own custom events and then provide callbacks for those. In this gallery plugin it might make sense to add an "onImageShow" callback.
-->
もしあなたのプラグインが何らかのイベントによって起動されるなら、それぞれのイベントにコールバックの機能を提供することは良い考えです。加えて、独自のカスタム・イベントを作成し、それらにコールバックを与えることも可能です。ギャラリー・プラグインの例では、"onImageShow" コールバックの追加が妥当でしょう。

<!--
```
var defaults = {

	// We define an empty anonymous function so that
	// we don't need to check its existence before calling it.
	onImageShow : function() {},

	// ... rest of settings ...

};

// Later on in the plugin:

$nextButton.bind( "click", showNextImage );

function showNextImage() {

	// Stuff to show the image here...

	// Here's the callback:
	settings.onImageShow.call( this );
}
```
-->
```
var defaults = {

	// 呼び出す前にその存在を確かめる必要がないよう、
	// 予め空の無名関数を与えておく。
	onImageShow : function() {},

	// ... 残りの設定 ...

};

// プラグインの後半：

$nextButton.bind( "click", showNextImage );

function showNextImage() {

	// ここには画像を表示するコード...

	// ここでコールバッック関数を呼び出す
	settings.onImageShow.call( this );
}
```

<!--
Instead of initiating the callback via traditional means (adding parenthesis) we're calling it in the context of `this` which will be a reference to the image node. This means that you have access to the actual image node through the `this` keyword within the callback:
-->
コールバックを従来のやり方で（括弧をつけて）呼び出さないのは、画像のノードへの参照を示す `this` のコンテキストで実行させるためです。こうすればコールバックの中では次のように、`this` キーワードを介して実際の画像のノードにアクセスすることができるのです。

<!--
```
$( "ul.imgs li" ).superGallery({
	onImageShow: function() {
		$( this ).after( "<span>" + $( this ).attr( "longdesc" ) + "</span>" );
	},

	// ... other options ...
});
```
-->
```
$( "ul.imgs li" ).superGallery({
	onImageShow: function() {
		$( this ).after( "<span>" + $( this ).attr( "longdesc" ) + "</span>" );
	},

	// ... 他のオプション ...
});
```

<!--
Similarly you could add an "onImageHide" callback and numerous other ones. The point of callbacks is to give plugin users an easy way to add additional functionality without digging around in the source.
-->
同様に "onImageHide" やその他多数のコールバックを追加することができます。コールバックのポイントは、プラグインのユーザーがソースコードを読み込まなくとも機能を追加する簡単な方法を提供することです。

<!--
### Remember, It's a Compromise
-->
### 妥協、それが肝心です

<!--
Your plugin is not going to be able to work in every situation. And equally, it's not going to be very useful if you offer no or very few methods of control. So, remember, it's always going to be a compromise. Three things you must always take into account are:
-->
あなたのプラグインは、どんな状況に対応できるようにすることがゴールではありません。また同時に、制御のメソッドが少な過ぎても役に立たないでしょう。そう、常に妥協が肝心なのです。常に次の3つを考慮に入れると良いでしょう：

<!--
- *Flexibility*: How many situations will your plugin be able to deal with?
- *Size*: Does the size of your plugin correspond to its level of functionality? I.e. Would you use a very basic tooltip plugin if it was 20k in size? – Probably not!
- *Performance*: Does your plugin heavily process the options in any way? Does this affect speed? Is the overhead caused worth it for the end user?
-->
- *柔軟性*： どれだけの状況に対応しようとしているのか？
- *サイズ*： プラグイン機能のレベルに照らして、サイズは適正か？ 非常にベーシックなツールチップのプラグインが20Kバイトだったら、誰も使いません！
- *パフォーマンス*： 必要以上に重たいオプションの処理をしていないか？ スピードに影響を与えていないか？ そのオーバーヘッドはエンドユーザーに悪影響を引き起こしていないか？
