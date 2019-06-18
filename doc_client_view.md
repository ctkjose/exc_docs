**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>


# Views #
*This topic applies to the Front-End framework.*

A view is a fragment of HTML like the following.

```html
<div class="view" name="myView" data-controller="myController">
	<b>My View Contents</b>
	...
</div>
```
An alternate format is to use a JSON string with the view definition.
```html
<div class="view" data-view='{"name":"myView", "controller":"myController", "type":"modal"}'>
	<b>My View Contents</b>
	...
</div>
```

You place the html fragment representing your `view` inside the `<body>` of your page. In our backend there are various ways to include a view, see [Building the UI](./doc_server_ui_views.md) for more details. Later we will also see how to add views dynamically from javascript.

When loading your app EXC will load the views it finds on your page. EXC will treat any DOM element with the css class `"view"` as an actual view.

While a `view` may be just plain html, in EXC a `view` is where we place `components`.


## The Stage ##

The [stage](./doc_client_stage.md) is where our views live. We use the `exc.stage` object to display our view.  Be sure to read the [stage](./doc_client_stage.md) documentation to lear more.

## View controller ##

You may have notice that our view's `<div>` have a `controller` attribute for example:
```HTML
<div class="view" name="myView" data-controller="myController">
```

Every view gets a [controller](./doc_client_controllers.md) assigned. By default a view is assigned to your `app` controller, unless you specify a different one.

The attribute `data-controller` specifies the name of the controller associated with a view.

The associated controller is able to handle events initiated by the view, its child components or events not handled by child views.

```js
self.loginController = {
	initialize: function(){
		console.log("@loginController.initialize()");
	},
	viewReady: function(msg){
		console.log("@loginController.viewReady() %o", msg);
	},
	loginUser_change: function(msg){
		//lets validate the user name...
	}
}
```

If an event is not implemented by a controller the event will eventually be received by your APP controller where it maybe handled or broadcasted as a message.

# Related Topics #

[Stage](./doc_client_stage.md)<BR>
[Controller](./doc_client_controllers.md)<BR>
