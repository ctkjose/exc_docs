[Frontend](./fte_index.md) {menu}
[Related]() {:menu}
[Controller's Manifest](./doc_client_controller_manifest.md){:menu}
[Interacting with the front-end](./doc_server_client.md){:menu}
**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Controller # {#ctldesc}

A **controller** is an object responsible of handling requests or events. In EXC we use controllers to implement the logic of our application.

A controller is a plain javascript object that EXC decorates with new functionality to become a full fledge `controller`.

What makes special a controller is the functions that we place inside our controller object.

The execution of your application is in essence your code responding to events (also called messages). We respond to events by adding event handlers, which are nothing more than functions in a controller object.

In a controller an event handler is a function, who's name follows a simple naming convention (an event handler signature). A function name that starts with "on" , for example `onAppStart` is considered a handler for the event `"AppStart"`.

```js
myController = {
	initialize: function(){
		//controller loaded, do my init
	},
	onAppStart: function(){
		console.log("App is running...");
		return true; //return false to cancel application run
	}
};
```
An event may be raised by actions, by DOM events, by your code, or by EXC itself. The great thing of controllers is that they make it super easy to decouple your application logic using events in a publish/subscribe fashion.

In addition to event handlers a controller can receive **messages**. You can think of message as just a way to invoking a function on your controller. A message handler is nothing more than a function with a name that matches the message name. EXC provides some easy ways to invoke messages and attach them to DOM events.

# App Controller # {#appctl}

The **app controller** is the main controller responsible of the application. In addition to our app controller we add other controllers to better structure our app logic, made the code more manageable and add reusability.

The app controller is responsible for the orchestration of your app's entire life-cycle and every application must have an app controller.

Since the app controller is actually the only controller required in an EXC app, we will use it as a starting point to learn about controllers.

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

A controller may implement an **initialize** function. The `initialize` method is called to let you initialize your controller. The `initialize` is generally invoked before the app is running.

> ! Be careful when making assumptions about what other things are loaded and available inside a `initialize` function of a controller. The only guarantee is that the controller object and its related scope is ready.

The app controller gets a lot of special events from EXC, most events are optional and you add event handlers for the ones that you need.

The **AppStart** event is a required event and it is called to ensure that EXC can RUN your app. This handler must return `true` to let the application run.

With these two our app controller knows looks like this:

```js
app = {
	initialize: function(){
		//controller loaded, do my init
	},
	onAppStart: function(){
		console.log("App is running...");
		return true; //return false to cancel application run
	}
};
```

## State Management ## {#ctlapppubsub}

As a Javascript application grows in complexity managing the information is difficult, mostly because state and information is scattered across many components and pieces.

In EXC the **app controller** is a global object that is available to all the different elements of your application and can conveniently help you coordinate the different aspects of your application logic and move data around.

Your app controller is available in the global scope using the variable `app` or `self.app` or `window.app`.

Your `app` implements a publisher/subscriber functionality which makes it very easy to orchestrate your app logic and allow disconnected elements to work together.

> **Just to be clear!** All controllers implement pub/sub functionality that you can use. The `app` controller is the only one used by EXC to broadcasts events around and the only one that provides automatic subscriptions from other controllers.

Lets see an example:

```js
myController = {
	initialize: function(){
		//controller loaded, do my init
	},
	onUserReady: function(user){
		console.log("We got a user [%s]...", user.username);
	}
};
//some where in your app...
app.publish("userReady", {"username": "ctk", "name": "Jose"} );
```

# Adding other controllers # {#addingotherctl}

Segregating our app logic with multiple controllers is always a good idea.

The following are the basic methods that you may use to load additional controllers in your front-end.

> **IMPORTANT** do not use these methods to load your `app` controller as all of these require the app controller to be running already.

A third option not discussed here is to load a controller yourself and using `exc.app.loadControllerByName(objName)` to make it available.

## Adding a controller with the Manifest ##
Your `app` controller (or any controller for the fact) may include a [manifest](./doc_client_controller_manifest.md) property. A [manifest](./doc_client_controller_manifest.md) is used to specify dependencies or additional assets used by a controller.

The [manifest](./doc_client_controller_manifest.md) property is a plain object that you add to your controller with items in either a `require` or `include` property. See [Controller's Manifest](./doc_client_controller_manifest.md) for more details.

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

See [appController](fte_ref_appcontroller.md) to learn more about your app controller.


## Adding a controller with a backend interaction ##

You PHP backend may inject a controller using `$client->addController()`:
```php
<php
$client->addController('loginController', 'asset://js/controller.login.js');
$client->done();
```

See [Interacting with the front-end](./doc_server_client.md) for more details.



# Related Topics #

[Controller's Manifest](./doc_client_controller_manifest.md)<BR>
