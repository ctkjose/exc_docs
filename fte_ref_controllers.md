**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Front-End Controllers #
*This topic applies to the Front-End framework.*

Your application is single page application ([SPA](https://en.wikipedia.org/wiki/Single-page_application)) that is orchestrated by controllers.

In your front-end there is you **app controller** that handles the execution of your application, and additional controllers that you add to handle different aspects of your application.

A controller is a plain javascript object that we decorate to become a full fledge `controller` in EXC.

# App Controller #

EXC uses a global object named `app` as your app controller.


When EXC is loading it will wait until a variable named `app` appears on the [global](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Global_variables) scope (In a browser thats the [window](https://developer.mozilla.org/en-US/docs/Web/API/Window) or [self](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Global_variables)).

The `app` object can be inline inside your HTML in a `<script>` tag or loaded dynamically.

```html
<html>
<head>...</head>
<body>
...
<script type='text/javascript'>
app = {
	initialize: function(){
		//app object was loaded as the app controller
	}
};
</script>
</body>
</html>
```

What makes special a controller is the functions that we place inside our controller object.

The execution of your application is in essence your code responding to events (also called messages). We respond to events by adding event handlers, which are nothing more than functions in our object.

In a controller an event handler is a function, who's name follows a simple naming convention (an event handler signature). A function name that starts with "on" , for example `onAppStart` is considered a handler for the event `"AppStart"`.

```js
self.app = {
	initialize: function(){
		//app object was loaded as the app controller
	},
	onAppStart: function(){
		console.log("App is running...");
	}
};
```

# Adding other controllers #

The following are the basic methods that you may use to load additional controllers in your front-end.
> **IMPORTANT** do not use these methods to load your `app` controller as all of these options require the app controller to be available to work.

A third option not discussed here is to load a controller yourself and using `exc.app.loadControllerByName(objName)` to make it available.

## Adding a controller with the Manifest ##
Your `app` controller (or any controller for the fact) may include a [manifest](./doc_client_controller_manifest.md) property. A [manifest](./doc_client_controller_manifest.md) is used to specify dependencies or additional assets used by a controller.

The [manifest](./doc_client_controller_manifest.md) is a plain object property that you add to your controller with items in either a `require` or `include` property. See [Controller's Manifest](./doc_client_controller_manifest.md) for more details.

```js
self.app = {
	manifest: {
		require: [
			{controller: "loginController", url: "../a/js/controller.login.js", wait: true},
		]
	},
	initialize: function(){
		//app object was loaded as the app controller
	},
	onAppStart: function(){
		console.log("App is running...");
	}
};
```


## Adding a controller with a backend interaction ##

The backend may inject a controller using `$client->addController()`:
```php
<php
$client->addController('loginController', 'asset://js/controller.login.js');
$client->done();
```

See [Interacting with the front-end](./doc_server_client.md) for more details.



# Related Topics #

[Controller's Manifest](./doc_client_controller_manifest.md)<BR>
