---
title: jQuery UI ウィジェット・ファクトリー型のステートフルなプラグインの書き方
level: intermediate
source: http://jqfundamentals.com/legacy
attribution:
  - jQuery の基礎
  - Scott González <scott.gonzalez@gmail.com>
---

<!--
## Writing Stateful Plugins with the jQuery UI Widget Factory
-->
## jQuery UI ウィジェット・ファクトリーでステートフルなプラグインを書く

<!--
While most existing jQuery plugins are stateless – that is, we call them on an element and that is the extent of our interaction with the plugin – there's a large set of functionality that doesn't fit into the basic plugin pattern.
-->
多くの jQuery プラグインはステートレス – 即ち、要素に対して呼び出し、プラグインでユーザーとのインタラクションを拡張するタイプ – ですが、こういったプラグインの基本パターンでは機能的に合わない場面も数多くあります。

<!--
In order to fill this gap, jQuery UI has implemented a more advanced plugin system. The new system manages state, allows multiple functions to be exposed via a single plugin, and provides various extension points. This system is called the Widget Factory and is exposed as `jQuery.widget` as part of jQuery UI 1.8; however, it can be used independently of jQuery UI.
-->
この溝を埋めるため、jQuery UI ではより高度なプラグイン・システムを採用しています。状態を管理し、1つのプラグインで複数の機能を公開し、様々な拡張点を提供する新しいシステムです。このシステムはウィジェット・ファクトリーと呼ばれ、jQuery UI 1.8 から `jQuery.widget` として公開されています。しかしながらこのシステムは、jQuery UI とは独立して使うことができます。

<!--
To demonstrate the capabilities of the Widget Factory, we'll build a simple progress bar plugin.
-->
ウィジェット・ファクトリーで出来ることを説明するために、ここではシンプルなプログレスバー・プラグインを作ることにしましょう。

<!--
To start, we'll create a progress bar that just lets us set the progress once. As we can see below, this is done by calling `jQuery.widget` with two parameters: the name of the plugin to create and an object literal containing functions to support our plugin.
-->
まずは、進捗を1回だけセット出来るプログレスバーをから始めましょう。以降で見ていく通り、これを実現するために2つの引数を指定して `jQuery.widget` を呼び出します：作成するプラグインの名称と、プラグインがサポートする関数のオブジェクト・リテラルです。

<!--
When our plugin gets called, it will create a new plugin instance and all functions will be executed within the context of that instance. This is different from a standard jQuery plugin in two important ways. First, the context is an object, not a DOM element. Second, the context is always a single object, never a collection.
-->
プラグインが呼ばれると、プラグインのインスタンスが新たに生成され、すべての関数がそのインスタンスのコンテキスト内で実行されるようになります。この仕組みには、標準的な jQuery プラグインと2つの重要な相違点があります。1つ目は、コンテキストが DOM 要素ではなくオブジェクトであるという点、そして2つ目は、コンテキストは常に単一のオブジェクトであり、集合ではないという点です。

<!--
A simple, stateful plugin using the jQuery UI Widget Factory:
-->
jQuery UI の ウィジェット・ファクトリーを使ったシンプルかつステートフルなプラグインの例：

```
$.widget( "nmk.progressbar", {

	_create: function() {
		var progress = this.options.value + "%";
		this.element.addClass( "progressbar" ).text( progress );
	}

});
```

<!--
The name of the plugin must contain a namespace; in this case we've used the `nmk` namespace. There is a limitation that namespaces be exactly one level deep – that is, we can't use a namespace like `nmk.foo`. We can also see that the Widget Factory has provided two properties for us. `this.element` is a jQuery object containing exactly one element. If our plugin is called on a jQuery object containing multiple elements, a separate plugin instance will be created for each element, and each instance will have its own `this.element`. The second property, `this.options`, is a hash containing key/value pairs for all of our plugin's options. These options can be passed to our plugin as shown here.
-->
プラグイン名には必ず名前空間が含まれなければなりません。上の例では名前空間として `nmk` を使いました。ただし名前空間には1階層までという制限があります – 即ち、名前空間として `nmk.foo` は使えないということです。またウィジェット・ファクトリーは、2つのプロパティを持っていることが分かります。`this.element` はただ1つの要素を含む jQuery オブジェクトです。プラグインが複数の要素を含む jQuery オブジェクトとして呼び出された場合は、それぞれの要素に対してそれぞれ別のプラグインのインスタンスが生成され、各インスタンスが自身の `this.element` を持ちます。2つ目のプロパティ `this.options` は、プラグインの全オプション表す key と value のペアを含むハッシュです。これらのオプションは、次に示すようにプラグインに渡されます。

<!--
*Note:* In our example we use the `nmk` namespace. The `ui` namespace is reserved for official jQuery UI plugins. When building your own plugins, you should create your own namespace. This makes it clear where the plugin came from and whether it is part of a larger collection.
-->
上の例では名前空間に `nmk` を使っています。`ui` という名前空間は正規の jQuery UI プラグイン用に予約されています。独自のプラグインを作成する場合には、独自の名前空間を作成します。これにより、プラグインが何に由来し、より大きなコレクションの一部かどうかを明らかにしてくれます。

<!--
Passing options to a widget:
-->
ウィジェットへのオプションを解釈する例：

```
$( "<div />" ).appendTo( "body" ).progressbar({ value: 20 });
```

<!--
When we call `jQuery.widget` it extends jQuery by adding a method to `jQuery.fn` (the same way we'd create a standard plugin). The name of the function it adds is based on the name you pass to `jQuery.widget`, without the namespace; in our case it will create `jQuery.fn.progressbar`. The options passed to our plugin get set in `this.options` inside of our plugin instance.
-->
`jQuery.widget` を呼び出すと、（標準的なプラグインを作る時と同様に）`jQuery.fn` にメソッドを追加して jQuery を拡張します。追加される関数の名前は、`jQuery.widget` に引き渡した名前を元にしますが、名前空間は付きません。今の例では `jQuery.fn.progressbar` です。プラグインに渡されるオプションは、プラグイン・インスタンスの `this.options` に集合として格納されます。

<!--
As shown below, we can specify default values for any of our options. When designing your API, you should figure out the most common use case for your plugin so that you can set appropriate default values and make all options truly optional.
-->
次に、特定のデフォルト値をオプションに設定する方法を示します。API を設計するときには、最も典型的なプラグインのユースケースを見つけるべきです。そうすれば、適切なデフォルト値を設定できますし、すべてのオプションをまさにオプショナルにすることが出来るでしょう。

<!--
Setting default options for a widget:
-->
ウィジェットにオプションのデフォルト値を設定する例：

<!--
```
$.widget( "nmk.progressbar", {

	// Default options.
	options: {
		value: 0
	},

	_create: function() {
		var progress = this.options.value + "%";
		this.element.addClass( "progressbar" ).text( progress );
	}

});
```
-->
```
$.widget( "nmk.progressbar", {

	// オプションのデフォルト値
	options: {
		value: 0
	},

	_create: function() {
		var progress = this.options.value + "%";
		this.element.addClass( "progressbar" ).text( progress );
	}

});
```

<!--
### Adding Methods to a Widget
-->
### ウィジェットにメソッドを追加する

<!--
Now that we can initialize our progress bar, we'll add the ability to perform actions by calling methods on our plugin instance. To define a plugin method, we just include the function in the object literal that we pass to `jQuery.widget`. We can also define "private" methods by prepending an underscore to the function name.
-->
さて、ここでプログレスバーを初期化してみましょう。プラグインのインスタンスに、アクションを実行するメソッドを追加します。プラグインのメソッドを定義するために、`jQuery.widget` に引き渡すオブジェクト・リテラルにその関数を含めます。先頭にアンダースコアを付けた "プライベートな" メソッドも同時に定義します。

<!--
Creating widget methods:
-->
ウィジェットのメソッドを作成する例：

<!--
```
$.widget( "nmk.progressbar", {
	options: {
		value: 0
	},

	_create: function() {
		var progress = this.options.value + "%";
		this.element.addClass("progressbar").text( progress );
	},

	// Create a public method.
	value: function( value ) {

		// No value passed, act as a getter.
		if ( value === undefined ) {

			return this.options.value;

		// Value passed, act as a setter.
		} else {

			this.options.value = this._constrain( value );
			var progress = this.options.value + "%";
			this.element.text( progress );

		}

	},

	// Create a private method.
	_constrain: function( value ) {

		if ( value > 100 ) {
			value = 100;
		}

		if ( value < 0 ) {
			value = 0;
		}

		return value;
	}

});
```
-->
```
$.widget( "nmk.progressbar", {
	options: {
		value: 0
	},

	_create: function() {
		var progress = this.options.value + "%";
		this.element.addClass("progressbar").text( progress );
	},

	// 公開するメソッドを作成する
	value: function( value ) {

		// 引数なしの場合には getter として動作させる
		if ( value === undefined ) {

			return this.options.value;

		// 引数がある場合には setter として動作させる
		} else {

			this.options.value = this._constrain( value );
			var progress = this.options.value + "%";
			this.element.text( progress );

		}

	},

	// プライベートなメソッドを定義する
	_constrain: function( value ) {

		if ( value > 100 ) {
			value = 100;
		}

		if ( value < 0 ) {
			value = 0;
		}

		return value;
	}

});
```

<!--
To call a method on a plugin instance, you pass the name of the method to the jQuery plugin. If you are calling a method that accepts parameters, you simply pass those parameters after the method name.
-->
プラグイン・インスタンスのメソッドを呼び出すには、jQuery プラグインにメソッド名を引き渡します。引数を受け取るメソッドを呼び出す場合には、それら引数をメソッド名に続けます。

<!--
Calling methods on a plugin instance:
-->
プラグイン・インスタンスのメソッドを呼び出す例：

<!--
```
var bar = $( "<div />" ).appendTo( "body").progressbar({ value: 20 });

// Get the current value.
alert( bar.progressbar( "value" ) );

// Update the value.
bar.progressbar( "value", 50 );

// Get the current value again.
alert( bar.progressbar( "value" ) );
```
-->
```
var bar = $( "<div />" ).appendTo( "body").progressbar({ value: 20 });

// value メソッド - 現在の値を取得する
alert( bar.progressbar( "value" ) );

// value メソッド - 値を更新する
bar.progressbar( "value", 50 );

// value メソッド - 再度、現在の値を取得する
alert( bar.progressbar( "value" ) );
```

<!--
*Note:* Executing methods by passing the method name to the same jQuery function that was used to initialize the plugin may seem odd. This is done to prevent pollution of the jQuery namespace while maintaining the ability to chain method calls.
-->
*注：* プラグインの初期化に使ったのと同じ jQuery 関数にメソッド名を引き渡して実行する方法は、奇妙に思えるかもしれません。これは、メソッド呼び出しのチェーンを可能にしつつ、jQuery の名前空間を汚染させないために行うものです。

<!--
### Working with Widget Options
-->
### ウィジェット・オプションの扱い方

<!--
One of the methods that is automatically available to our plugin is the option method. The option method allows you to get and set options after initialization. This method works exactly like jQuery's `.css()` and `.attr()` methods: you can pass just a name to use it as a setter, a name and value to use it as a single setter, or a hash of name/value pairs to set multiple values. When used as a getter, the plugin will return the current value of the option that corresponds to the name that was passed in. When used as a setter, the plugin's `_setOption` method will be called for each option that is being set. We can specify a `_setOption` method in our plugin to react to option changes.
-->
プラグインで自動的に得られるメソッドの1つが、オプション・メソッドです。オプション・メソッドは、初期化後も値の取得や設定が出来ます。このメソッドは、ちょうど jQuery の `.css()` や `.attr()` メソッドと全く同様に機能します：名前を引き渡して getter として使うことも出来ますし、名前と値を引き渡して単一の setter としたり、名前と値の組をハッシュとして複数の値を設定することも出来ます。getter として使われる場合は、引き渡された名前にひも付けされたオプションの値を返すでしょう。setter として使われる時は、設定すべきそれぞれのオプションにプラグインの `_setOption` メソッドが呼び出されるでしょう。オプションの変更に対応するために、プラグインには `_setOption` メソッドを定義しておきます。

<!--
Responding when an option is set:
-->
オプションが設定された時に反応する例：

```
$.widget( "nmk.progressbar", {

	options: {
		value: 0
	},

	_create: function() {
		this.element.addClass( "progressbar" );
		this._update();
	},

	_setOption: function( key, value ) {
		this.options[ key ] = value;
		this._update();
	},

	_update: function() {
		var progress = this.options.value + "%";
		this.element.text( progress );
	}

});
```

<!--
### Adding Callbacks
-->
### コールバックの追加

<!--
One of the easiest ways to make your plugin extensible is to add callbacks so users can react when the state of your plugin changes. We can see below how to add a callback to our progress bar to signify when the progress has reached 100%. The `_trigger` method takes three parameters: the name of the callback, a native event object that initiated the callback, and a hash of data relevant to the event. The callback name is the only required parameter, but the others can be very useful for users who want to implement custom functionality on top of your plugin. For example, if we were building a draggable plugin, we could pass the native mousemove event when triggering a drag callback; this would allow users to react to the drag based on the x/y coordinates provided by the event object.
-->
プラグインを拡張できるようにしておく最も簡単な方法の1つは、プラグインの状態が変化した時にユーザーが対応できるようにコールバックを追加することです。プログレスバーが100%に達したことが分かるようにコールバックを追加する方法を次の例で見てみましょう。`_trigger` メソッドは、次の3つの引数を受け取ります：コールバックの名前、コールバックを起動するネイティブなイベント・オブジェクト、そしてそのイベントに結びつけられるデータのハッシュです。コールバックの名前だけが必須の引数ですが、プラグインにカスタムな機能を実装したいと考えるユーザにとっては、残りの引数が大変役に立ちます。例えばドラッグ可能なプラグインを作るとしましょう。ドラッグのコールバック起動時にネイティブな `mousemove` イベントを引き渡せば、そのイベント・オブジェクトから得られるx/y座標に基づいてドラッグに対応できるようになるのです。

<!--
Providing callbacks for user extension:
-->
ユーザーが拡張できるコールバックを提供する例：

```
$.widget( "nmk.progressbar", {

	options: {
		value: 0
	},

	_create: function() {
		this.element.addClass( "progressbar" );
		this._update();
	},

	_setOption: function( key, value ) {
		this.options[ key ] = value;
		this._update();
	},

	_update: function() {
		var progress = this.options.value + "%";
		this.element.text( progress );
		if ( this.options.value == 100 ) {
			this._trigger( "complete", null, { value: 100 } );
		}
	}

});
```

<!--
Callback functions are essentially just additional options, so you can get and set them just like any other option. Whenever a callback is executed, a corresponding event is triggered as well. The event type is determined by concatenating the plugin name and the callback name. The callback and event both receive the same two parameters: an event object and a hash of data relevant to the event, as we'll see below.
-->
コールバック関数は基本的にはただの追加オプションなので、他のオプション同様、取得したり設定したりすることが出来ます。コールバックが実行されると、関連するイベントも起動されます。イベントの種類は、プラグイン名とコールバック名を連結して決定されます。コールバックとイベントは両方とも同じ2つの引数を受け取ります：以下の例に示す通り、イベント・オブジェクトとそのイベントに関連したデータのハッシュです。

<!--
If your plugin has functionality that you want to allow the user to prevent, the best way to support this is by creating cancelable callbacks. Users can cancel a callback, or its associated event, the same way they cancel any native event: by calling `event.preventDefault()` or using `return false`. If the user cancels the callback, the `_trigger` method will return false so you can implement the appropriate functionality within your plugin.
-->
ユーザーが中断したいと思った時の機能を持たせるには、キャンセルのコールバックを追加できるようにしておくのが最も良い方法です。`event.preventDefault()` を呼び出すか `return false` を使って、ユーザーはコールバックやネイティブなイベントと同様に関連するイベントのキャンセルが出来るようになります。ユーザーがコールバックをキャンセルをした場合、`_trigger` メソッドが `false` を返すので、プラグインとして正しい実装となるのです。

<!--
Binding to widget events:
-->
ウィジェットのイベントをバインドする例：

<!--
```
var bar = $( "<div />" ).appendTo( "body" ).progressbar({

	complete: function( event, data ) {
		alert( "Callbacks are great!" );
	}

}).bind( "progressbarcomplete", function( event, data ) {

 	alert( "Events bubble and support many handlers for extreme flexibility." );

 	alert( "The progress bar value is " + data.value );

});

bar.progressbar( "option", "value", 100 );
```
-->
```
var bar = $( "<div />" ).appendTo( "body" ).progressbar({

	complete: function( event, data ) {
		alert( "Callbacks are great!" );
	}

}).bind( "progressbarcomplete", function( event, data ) {

	alert( "可能な限りの柔軟性を持たせるため、イベントのバグリングと多くのハンドラーをサポートします" );

	alert( "プログレスバーの値は " + data.value );

});

bar.progressbar( "option", "value", 100 );
```

<!--
### The Widget Factory: Under the Hood
-->
### ウィジェット・ファクトリー：その内部

<!--
When you call `jQuery.widget`, it creates a constructor function for your plugin and sets the object literal that you pass in as the prototype for your plugin instances. All of the functionality that automatically gets added to your plugin comes from a base widget prototype, which is defined as `jQuery.Widget.prototype`. When a plugin instance is created, it is stored on the original DOM element using `jQuery.data`, with the plugin name as the key.
-->
`jQuery.widget` を呼び出すと、プラグイン用のコンストラクタ関数を作成し、引き渡したオブジェクト・リテラルをプラグイン・インスタンスのプロトタイプとして設定します。プラグインに自動的に追加されるすべての機能は、`jQuery.Widget.prototype` として定義されるウィジェットのプロトタイプに由来します。プラグインのインスタンスが生成されると、キーとしてプラグインの名称と共に `jQuery.data` によって元の DOM 要素上に保存されます。

<!--
Because the plugin instance is directly linked to the DOM element, you can access the plugin instance directly instead of going through the exposed plugin method if you want. This will allow you to call methods directly on the plugin instance instead of passing method names as strings and will also give you direct access to the plugin's properties.
-->
このようにプラグインのインスタンスは DOM 要素に直接結びつけられているため、もし望むなら、公開されたメソッドの代わりにプラグインのインスタンスを通して直接アクセスすることも出来ます。このため、メソッド名を文字列として引き渡してプラグインのインスタンスにアクセスする代わりに、直接そのメソッドを呼び出ししたり、あるいはその属性に直接アクセスすることが出来るます。

<!--
```
var bar = $( "<div />")
	.appendTo( "body" )
	.progressbar()
	.data( "progressbar" );

// Call a method directly on the plugin instance.
bar.option( "value", 50 );

// Access properties on the plugin instance.
alert( bar.options.value );
```
-->
```
var bar = $( "<div />")
	.appendTo( "body" )
	.progressbar()
	.data( "progressbar" );

// プラグインのインスタンスからメソッドを直接呼び出す
bar.option( "value", 50 );

// プラグインのインスタンスから属性を直接アクセスする
alert( bar.options.value );
```

<!--
One of the biggest benefits of having a constructor and prototype for a plugin is the ease of extending the plugin. By adding or modifying methods on the plugin's prototype, we can modify the behavior of all instances of our plugin. For example, if we wanted to add a method to our progress bar to reset the progress to 0% we could add this method to the prototype and it would instantly be available to be called on any plugin instance.
-->
プラグインにとってコンストラクタとプロトタイプを持つことの最大の利点の1つは、プラグインの拡張を容易にすることにあります。プラグインのプロトタイプにあるメソッドを追加したり修正したりすることによって、そのインスタンスの振る舞いを変えられます。例えば、プログレスバーを0%にリセットするメソッドを追加したい場合には、このメソッドをプロトタイプに追加するだけで、そのインスタンスからすぐに呼び出し可能となるのです。

```
$.nmk.progressbar.prototype.reset = function() {
	this._setOption( "value", 0 );
};
```

<!--
### Cleaning Up
-->
### インスタンスのクリア

<!--
In some cases, it will make sense to allow users to apply and then later unapply your plugin. You can accomplish this via the `destroy` method. Within the `destroy` method, you should undo anything your plugin may have done during initialization or later use. The `destroy` method is automatically called if the element that your plugin instance is tied to is removed from the DOM, so this can be used for garbage collection as well. The default `destroy` method removes the link between the DOM element and the plugin instance, so it's important to call the base function from your plugin's `destroy` method.
-->
プラグインを利用した後に、その解除ができると良い場合があるでしょう。これには `destroy` メソッドが使えます。`destroy` メソッドの内部では、初期化やその後にプラグインが実行したことすべてを元に戻すべきでしょう。プラグインのインスタンスと結びついている DOM 要素が削除されると `destroy` メソッドが自動的に呼び出され、ガベージコレクションの対象となります。定義済みの `destroy` メソッドは DOM 要素とプラグインとの結びつきを削除するので、プラグインの `destroy` メソッドからその基底関数を呼び出すことが重要です。

<!--
Adding a destroy method to a widget:
-->
ウィジェットに `destroy` メソッドを追加する

<!--
```
$.widget( "nmk.progressbar", {

	options: {
		value: 0
	},

	_create: function() {
		this.element.addClass("progressbar");
		this._update();
	},

	_setOption: function( key, value ) {
		this.options[ key ] = value;
		this._update();
	},

	_update: function() {
		var progress = this.options.value + "%";
		this.element.text( progress );
		if ( this.options.value === 100 ) {
			this._trigger( "complete", null, { value: 100 } );
		}
	},

	destroy: function() {
		this.element
			.removeClass( "progressbar" )
			.text( "" );

		// Call the base destroy function.
		$.Widget.prototype.destroy.call( this );
	}

});
```
-->
$.widget( "nmk.progressbar", {

	options: {
		value: 0
	},

	_create: function() {
		this.element.addClass("progressbar");
		this._update();
	},

	_setOption: function( key, value ) {
		this.options[ key ] = value;
		this._update();
	},

	_update: function() {
		var progress = this.options.value + "%";
		this.element.text( progress );
		if ( this.options.value === 100 ) {
			this._trigger( "complete", null, { value: 100 } );
		}
	},

	destroy: function() {
		this.element
			.removeClass( "progressbar" )
			.text( "" );

		// 基底の destroy 関数を呼び出す
		$.Widget.prototype.destroy.call( this );
	}

});
```

<!--
### Conclusion
-->
### まとめ

<!--
The Widget Factory is only one way of creating stateful plugins. There are a few different models that can be used and each have their own advantages and disadvantages. The Widget Factory solves lots of common problems for you and can greatly improve productivity, it also greatly improves code reuse, making it a great fit for jQuery UI as well as many other stateful plugins.
-->
ウィジェット・ファクトリーはステートフルなプラグインを作成する唯一の方法です。実際に使わるこれとは異なるモデルも幾つかはありますが、それらには長所・短所があります。ウィジェット・ファクトリーは多くの共通した問題を解決し、生産性を大幅に向上させます。コードの再利用性を劇的に向上させ、他の多くのステートフルなプラグイン同様、jQuery UI との親和性を飛躍的に高めることができるのです。
