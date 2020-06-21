[Frontend](./fte_index.md) {menu:}
[Related]() {menu:}
[Controller's Manifest](./doc_client_controller_manifest.md){menu:}
[Interacting with the front-end](./doc_server_client.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# WIRE #
*This topic applies to the Front-End framework.*

# Concepts # {#concepts}

## Application Composition and Control ##

EXC attempts to simplify application development following many design patterns found under the principle of [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control) (IOC). Inversion of Control attempts to solve software architecture problems related to the structure of an applications components (libraries, services, etc) and how we wire those things together and manage dependencies.

While you may read about IOC mainly in the context of decoupling dependencies (dependency inversion), IOC is actually a matter of strategies to abstract and decouple the control flow of an application.

In EXC we use dependency inversion, control flow composition, event-driven logic among other abstractions. Of course one important goal is to have an easy to use implementation that people can learn and master quickly. EXC's approach is far from what you find in other frameworks like Java Spring, etc, but more in the line to cujojs.

**Decoupling** is a big deal In all of this, but beyond the interoperability of different components (wiring together, hint!) it offers other advantages:

- It create abstractions for the way we connect, consume components and even structure the applications logic. This abstractions can prove far more flexible that REST-API and other design strategies.
- It decouples the components allowing to work on smaller and more manageable code that is easy to refactor and debug. It allows for easier reusability and composition.
- It makes your code base more resilient to changes.

# Wire # {#wire}

In EXC the **wire** is the library used to implement application composition and control. While not too original, the `wire` comes from the notion of "wiring" our app together.

A wire can be used to do composition of components, the application or represent a logical flow.

## Wire Specification ##

EXC implements a simple [DSL](#dslspec) (domain-specific-language) to represent the compositions and abstractions supported by WIRE.

A DSL is always represented by a Javascript object literal or JSON object. The js object that represents a WIRE DSL is called a specification or "spec". A spec is a declarative object that can represent a lot of things.

A spec defines and make things happen by means of verbs. A verb is a special key or object structure that tells the WIRE to do something. A verb may load resources and libraries, create components, set some bindings for events and data, bootstrap an application and kick its execution, among others.

You will see verbs like `$ref`, `$emit`, `$load`, these "verbs" give meaning to the plain JS object that a spec is. EXC defines a core set of verbs but other plugins or modules may add their own verbs.

## Verbs ##

A verb is an abstraction that allows to interact and execute operations provided by different components and libraries without and explicit interaction or connection. Verbs are used to decouple interaction of different layers in your application.

A verb like everything else in the spec is a Javascript object literal or JSON object with a key name that starts with a dollar sign "$".

## References ## {#ref}

A reference is a verb entry identified with a `$ref` key (similar to the [JSON REF](https://tools.ietf.org/id/draft-pbryan-zyp-json-ref-03.html)) used to bind a value to a property inside an object.


A `$ref` is meant to resolve a value (to lookup somewhere) and are no intended as basic variables. A reference may be used as an assigment (binding) or as a parameter to another verb.

The common use of `$ref` is as a binding assigment:


```js
{
	aValue : { $ref: 'mycomponent.value'}
}
```

Values binded by a `$ref` assigment will be live, that is its value is update in many scenarios.


## DOM References ##

A spec may reference a DOM element by using a `$node:` resolver in a `$ref`:

```js
{
	aNode : { $ref: '$node:.myselector'}
}
```

This entry will add a property `aNode` to the object that points to the first DOM element matched by the selector.

We may also specify an `at` key with a selector pointing to the parent element where the element is:

```js
{
	aNode : { $ref: '$node:.myselector', at:'div.parent'}
}
```


## Components ## {#vcreate}

A component is an object representing a collection of functionality and logic that we can reference by its name. A component can also be an object of a library or module that you loaded see verbs [$load](#vload) and [$require](#vrequire).

When the spec has an object with a `$create` verb a property of type component is created.  For example:
```js
{
	myComponent: {
		$create: {
			//crerate options go here....
		}
	}
}
```
In this case the name of the component is "myComponent" and `myComponent` becomes a property of the current wire context.

The `$create` key is a plain object with options to create our component. An empty `$create` will use a plain object as a start.

We can use a factory function to return an object to be use as the basis of our component or a constructor function. The difference being that a constructor function is called with `new` and a factory function is just invoked and expected to return an object.


```js
{
	myComponent: {
		$create: {
			factory: buildFancyObject, //call a factory function
		}
	}
}
```
```js
{
	myComponent: {
		$create: {
			constructor: fancyObject, //call a constructor function
		}
	}
}
```

Another option is to use a CommonJS module that exports a single object.

```js
{
	myComponent: {
		$create: {
			//loads a module and assigns its export as the base object
			module: "./js/myFancyObject.js",
		}
	}
}
```

> ? When using the option `module` the component creation may be delayed until the module is loaded.

If you use a `$load` verb to pre-load content then you can use the `require` option to just get a reference to an existing export.

```js
{
	myComponent: {
		$create: {
			//assigns an existing export as the base object
			require: "./js/myFancyObject.js",
		}
	}
}
```


### Extending the component ###

Key value pairs other than the `$create` verb as procesed as regular WIRE specs in doing so we can extend and combine functionality into a component with ease.


#### Adding message handlers to the component. ####


```js
myComponent: {
	$create: {
		//Our create options ...
	},

	//add a handler for the "DoThis" message
	//with a traditional callback function
	onDoThis: function(msg){
		console.log("@myComponent.doThis()");
		console.log(msg);
		console.log("This=%o", this); //this points to myComponent

		//get the parent context of myComponent
		console.log("This.parent=%o", this.parent);
	}
},
```

A message handler may also point to a set of actions:
```js
myComponent: {
	//...

	//add a handler for the "DoThis" message
	//with a traditional callback function
	onDoThis: [
		{$emit: "doSomethingElse"},
		{$emit: "doSomethingElse"},
	]
},
```


#### Adding properties to the component. ####


```js
myComponent: {
	$create: {
		//Our create options ...
	},

	//add an observable property
	firstName: "Jose"
},
```
A key like `firstName: "Jose"` in the component's spec will create a property in the component created. We can access a property from a component like traditional variable.

```js
myComponent: {
	$create: {
		//Our create options ...
	},

	firstName: "Jose",	//add property
	onDoThis: function(msg){
		console.log("First Name is %s", firstName);
	}
},
```

Properties created in a spec are binded values that are also observable through the wire.

```js

	component1.wire.observeKey("firstName", aCallBack)

```

### Other options for the $create verb ###


The keys `done` and `error` can specified a function or a spec to be executed when the component is created succesfully or if ther was an error. The `done` option is particularly usefull to initiate or trigger a chain of operations.

```js
{
	myComponent: {
		$create: {
			module: "./js/module3.js",
			//a wire with actions to execute if the component is created
			done: [
				{$emit:"createAppMenus"},
				function(){ //a plain callback
					console.log("doing something!");
					//this is done, parent is myComponent
					console.log("Name=%s", this.parent.name);
				}
			]
		},
		onCreateAppMenus: function(msg){
			//some code here...
		}
	},
}
```


## Verb $emit ## {#emit}

**Verb:** $emit **Actionable:** Yes **Type:** Verb-Key

The `$emit` verb will emit a message in the application's service bus. The verb is

```js
{$emit: "message_name"}
```
Other keys provided to an `$emit` verb become values passed in the message's data.

```js
{$emit: "setTitle", fname:"Jose", lname:"Cuevas" }
```

Somewhere in a message handler we use the `message.data` to access the values:
```js
onSetTitle : function(msg){
	console.log( "Hello %s %s", msg.data.fname, msg.data.lname);
}
```

## Verb $do ## {#vdo}
**Verb:** $do **Actionable:** Yes **Type:** Verb-Key

The `$do` verb will let you specify an action or list of actions to execute.

```js
{$do: [
	{$emit: "message_name"},
	function(){
		console.log("executing a plain function...");
	}
]}
```

The value of a `$do` key is either a function or an array of actionable items.

## Verb $require ## {#vrequire}
**Verb:** $require **Actionable:** No **Type:** Verb-Key

Use  the `$require` verb to add an existing object, module or spec to a wire.

> ! `$require` WILL not load a module, the module must be already loaded with the `$load` verb or other means. We encourage you to manage your dependencies using a builder or packing utility.

```js
{
	//asumes exports is the object representing the library
	myLibrary: {$require: "./js/mylib.js"}
}
```

```js
{
	//specify with get which export to assign.
	myLibrary: {$require: "./js/mylib.js", get:"UIAdapter"}
}
```

When the option `get` is not used $require assumes that your export points to the library, unless:

- The module has an exports named "spec" in which case `exports.spec` will be loaded as an specification to create a wire.
- The module has an exports named "component" in which case `exports.component` will be created as a [component](#vcreate).

## Verb $load ## {#vload}
**Verb:** $load **Actionable:** Yes **Type:** Verb

The `$load` verb is a top level key that you specify to fetch resources (css, js) and load modules.

You can specify two set of assets to process with **include** and **require**. Includes are assets to be loaded but we do not care the order or if the app is unable to load them. A require in the other hand must be loaded for the `$load` to be successful, they are loaded sequentially and you may specify if the loading must wait for the current item before loading the next (this is useful for dependancies).

```js
{
	$load: {
		include: [
			//items to load...
		],
		require: [
			//items that must be loaded...
		]
	}
}
```

An entry in `include` or `require` may by a string with a url or a plain object with the assets options.

When specifying the assets options the following keys are available:

| Key | Example | Description |
| -- | -- | -- |
| url | `url: "./a/url/myfile.js"` | A string with the url of the file. You must use the `type` key when the system may not infer if the resource is a js or css file. **Required** |
| is_required | `is_required: true` | A boolean that indicates if this item is required to be loaded. Default is true. |
| is_forced | `is_forced: true` | A boolean that indicates that the resource must be reloaded ignoring any cache. Default is false. |
| type | `type: "js"`<br>`type: "css"`<br>`type: "json"` | A string indicating the resource type. |
| use |  `use: "aGlobalObject"`<br>`use: "anExportName"` | Adds an object or module to the current wire or context. Depending on the js file loaded you can specify a string with the name of a global object loaded or the name of an exported (module.exports) object. |
| wire | `wire: true` | Used to indicate that the object loaded with the `use` key must be treated as an specification to create a wire. |

Some examples:
```js
{
	$load: {
		include: [
			"./css/client_style.css"
		],
		require: [
			"./js/basic.js",
			{url:"./js/config.js", is_forced: true},
			{url:"./js/corelib.js"},
			{url:"./js/node_modules/mylib.js", use:"aLib"},
			{url:"./js/spec.js", use:"aspec", wire:true}
		]
	}
}
```

A `$load` may have a `done` option with actionable items to run when the loading is completed and in the opposite side an `error` option with actionable items will be execute when the loading fails:

```js
{
	$load: {
		require: [
			//items that must be loaded...
		],
		done: [
			{$emit: "processConfig"}
		],
		error: [
			{$emit: "appFailedToLoad"}
		]
	}
}
```

A `paths` option can be added to config path aliases.

```js
{
	$load: {
		require: [
			{url:"myLib"},
			{url:"modules/alib.js"}
		],
		paths: {
			'myLib': './js/include1.js',
			'modules': './js',
		},
	}
}
```





# Wire DSL # {#dslspec}

EXC implements a simple DSL (domain-specific-language) to represent the compositions and abstractions supported by WIRE.

A DSL is always represented by a Javascript object literal or JSON object. The js object that represents a WIRE DSL is called a specification or "spec".


| TERM | SYNTAX | DESCRIPTION |
| -- | ---- | -- |
| PRIMITIVE | | A literal string, number, boolean or date, regex. |
| OBJECT | | A JS object other than an Array |
| ARRAY | | A JS array object. |
| KEY | | Any valid key that may appear on the left side of a key-value pair in a JS literal object or JSON. |
| VALUE | PRIMITIVE<br>OBJECT<br>ARRAY | Any valid value that may appear on the right side of a key-value pair in a JS literal object or JSON. |
| PARAMS | `PARAM1:VALUE1, ...` | Set of key value pairs in a JS literal object or JSON |
| VERB_NAME | `$name`<br>`"$name"` | A key name that starts with the "$" sign. |
| VERB | `VERB_NAME : {...}` | A JS literal object or JSON that represents a WIRE composition. It appears as a key-value pair, the key is always a VERB_NAME and the right side is the argument/parameters passed to said verb. |
| VERB_KEY | `{VERB_NAME: VALUE, PARAMS}` | A JS literal object or JSON that represents a WIRE composition. A VERB_KEY may appear as the right side of a key-value pair or as an item in an array. Only one VERB_NAME may by present in the object, all other key-value pairs are arguments for the verb. |
| PROPERTY | `KEY : VALUE` | A traditional key-value pair is converted to a property of the object created or the actual context. |
| ACTION | `VERB-KEY`<br>`FUNCTION`<br>`ACTIONS`<br> | An actionable value. |
| ACTIONS | `[...]` | An array that may include: `VERB-KEY`, `FUNCTION`. |
| MSG_HANDLER | `onMessageName : FUNCTION`<br>`onMessageName : ACTIONS` | A key who's name starts with "on" followed by message name, becomes a message handler. |
