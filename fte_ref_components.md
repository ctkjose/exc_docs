**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Components #

In EXC components are used to provide an enhanced behavior to existing HTML controls and to create new controls and UI elements.

EXC comes with a set of built-in components and you may create your own.

## Adding a component to your HTML with a shorthand tag ##

Use the html tag `<w></w>` as a shorthand to add a component to your html.

The shorthand tag `<w>`  only requires two attributes, each component may require additional attributes.

| Attribute | Description |
| -- | -- |
| type | **Required** A component type identifier. |
| name | **Required** A name for this widget. |

For example:
```html
<w type="textbox" name="username" default="" suffix='<i class="la la-user"></i>' attr='{"placeholder":"Username1"}'></w>
```

Here is an alternate syntax using the attribute `def` with a JSON payload:
```HTML
<w def='{"type":"textbox", "name":"username", "default":"","attr":{"placeholder":"Username"}, "suffix":"<i class=\"la la-user\"></i>"}'></w>
```

## Getting a reference to a component instance ##

In JS a component instance is nothing more than a HTML element decorated with additional functionality.

To obtain an instance we use the name of element:
```js
	var widgetUsername = exc.component.get("fldUsername");
	var value = widgetUsername.getValue();
```

Actually in addition to the name you can pass an html element or a string with a selector to `exc.component.get()`.

> * This function uses the attribute `w` to determine what kind of component is represented by an element. For some other cases the function `exc.component.get()` takes an optional second argument with the id of a component to be returned.

The function `exc.component.get()` will return `undefined` if not found. If a selector matches more than one element it will return the first matched element.



## Component textbox ##

| Property | Description |
| -- | -- |
| type | **Required** Value is "textbox" |
| name | **Required** A name for this widget. |
| disabled | Make it disabled |
| help | Help or Alt/Title  attribute of the textbox. |
| width | Set a fixed width. Value must include a css unit, for example "100px". |
| size | Size attribute of textbox. |
| placeholder | Placeholder attribute of the textbox. |
| prefix | Decorates a textbox with an icon or text at the beginning. |
| suffix | Decorates a textbox with an icon or text at the end. |

### Value Modifiers ###

A textbox supports value modifiers to control the value returned by the widget.

| Name | Type | Description |
| -- | -- | -- |
| .value-use-suffix | class | The value returned by this widget will have its suffix appended. (See: attribute `suffix` ) |
| m-value-max | attribute | Max numeric value that can be returned. Ex: `m-value-max="5000"` |
| m-value-min | attribute | Min numeric value that can be returned. Ex: `m-value-min="1000"` |
| m-value-strip | attribute | Strips the string matched in the RegEx given. Ex: `m-value-strip="(\@.*)$"` |
| m-value-round-up | attribute | Rounds up the value to the given decimal precision. Ex: `m-value-round-up="2"` |
| m-value-round-down | attribute | Rounds down the value to the given decimal precision. Ex: `m-value-round-down="2"` |
| m-value-with-decimals | attribute | Formats number with the given number of decimals. It rounds up when required, except when 0 decimals is specified, in which case decimals are truncated. Ex: `m-value-with-decimals="2"` |
| m-value-with-suffix | attribute | Appends the string given to the value returned. |
| m-value-without-suffix | attribute | Removes the string given from the end of the value returned. |



# Defining a Component #

A new component is made available by registering its definition. This is done by calling:

```js
exc.components.register(myComponentDefinition);
```

## Component Definition ##

A component definition is an object with properties that define your component and some callback handlers to render the html and implement its behavior.

The following are the properties of a component definition:

| Name | Type | Declaration | Description |
| -- | -- | -- | -- |
| id | string | | **Required** The name of this component. It must be unique and only use alphanumeric characters and the underscore. <br><br>For example:<br> `id: "textbox"` |
| selectors | array of strings | | **Required** A list of selectors that identify or match this component.<br><br>For example:<br> `selectors: [ "[w='textbox']" ]` |
| setter | function | function(node, value) | **Optional** Called to set the value of the widget represented by this component.  |
| getter | function | function(node) | **Optional** Returns the value of the widget represented by this component. |
| vs | string or plain object |  | **Optional** A string with the name of a build in value provider interface (eg: `"value"`, `"attr"`, `"hidden"`) or a plain object with a `getter` function and a `setter` function.<br><br>The signature of a `getter` function is `function(elm)` and must return a value.<br><br>The signature of a `setter` function is `function(elm, value)`. <br><br>You may use the `getter` or `setter` functions instead. |
| isContainer | boolean | | **Optional** Indicates that this component is a container for other components or HTML content. Default value is false. |
| inherits| array of strings | | **Optional** Name of other components from where we get functionality.<br><br>Example: `inherits: ['container']` |
| fn | plain object | | **Optional**  Additional functionality implemented by an instance of this component. The functions in this object will be added to the element. |
| onInit | callback | function(node) | **Optional** Called to initialize the element that represents this component. <br><br>This function should be used to perform post processing that requires you to have the DOM of the component created. You may add event listeners required to implements the component's behavior. |
| onInstance | callback | function(node) | **Optional**  Called when an instance of this component was referenced. |
| build | callback | function(propertyBag) | **Optional** Called when the component must build the corresponding HTML representation for a widget.<br><br>This function is called before anything is injected in the actual DOM.<br><br>The argument propertyBag is an object with the properties and attributes of the widget requested. |


### A propertyBag ###

When using a shorthand tag or creating instances of widgets/components dynamically from JS we use a **propertyBag** with all the properties and attributes of the desired widget.

EXC will create a `propertyBag` for the attributes in a shorthand tag `<w></w>` that will be passed to a `build()` calback in a component definition.

The following snippet shows some of the `propertyBag` functionality, for more details you should check some of the built-in components in the code repository for reference.

```js

	var name = propertyBag.name;
	//getting a property
	if( propertyBag.hasProperty("size") ){
		var size = propertyBap.property("size");
	}

	//copy css classes, "data-" attributes and exc's "m-" attributes to a node
	propertyBap.copyDefaultAttributes(node);
```


Example definition:
```js
exc.components.register({
	id: 'textbox',
	selectors: ["[w='textbox']"],
	vs: "value", //value source

	fn : { //a plain object of functions to be added to a widget instance
		play: function(){
			console.log(this.uiw);
			console.log("at play...");
		}
	},
	build: function(e){
		//Build a widget node, from the widget properties passed on e

		//basic things that should be done on build:
		e.copyDefaultAttributes(t);
		//exc.components.installValueInterface(t,"value", this); //sets a value interface

		//return a single node or an array of nodes like [ node1, node2]
		return node;
	}
	onInit: function(o){
		//Initialize the component's contents, called after a widget is built or a widget node is found

	}
	onInstance: function(o){
		//A widget reference is requested for the node (instance of this component).
		//decorate or add special functionality, beyond what is defined in fn
	}
}
```


# More obscure details :) #

## The w attribute ##

An html element in your DOM that corresponds to a component is tagged with the attribute `w` that has the id of the component it represents.
