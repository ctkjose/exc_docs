**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Server-side Controllers #

The bread and butter of the backend are controllers, we use controllers to implement the logic of your app.

There are different types of controllers each with additional functionality depending on your type of application or tasks. You will create instances of these controllers to implement your app.

The main controller is the [appController](./doc_server_appcontroller.md) that manages the application lifecycle. Every application has an instance of an `appController`.

Additionally you may add instances of `viewController` for UI based applications and `processController` for services and cli applications.

# Creating a controller #

A controller is a class that extends one of the existing controller types. For example:

```php
<?php
class recordController extends \exc\controller\viewController {
	public function initialize(){
		//this is called when EXC creates the instance of your controller
	}
	public function onAction_main(){
		//this is our default action, when no particular action is specified in an interaction with our front-end
	}
	public function onAppInit(){
		//this is a message function
		//this function will be executed when an ```appInit``` message is published
	}
}
?>
```

The name of the class always ends with the suffix `"Controller"`. In the above example the controller name is `"record"` and the corresponding class name is `recordController`.

We put our controller class in a file with a name that follows this convention `controller.name.php`, for example: `controller.record.php`. The name of the file is important to allow EXC to find your controller.


## Handling messages ##

In EXC the app logic is orchestrated by means of [messages](./doc_server_events.md). The main job of a controller is to handle a given message or broadcast a message.

A message handler is a public function in a controller. Messages are explained in detail [here](./doc_server_events.md).

> * In your application the `appController` is responsible for broadcasting messages. Since all controllers are [event emitters](./doc_server_events.md) you can use them to create your own message based logic which is great to decouple applications.

In a controller a message handler is a function, who's name follows a simple naming convention (a message handler signature).

A function name that starts with **"on"** , for example `onAppStart` is considered a handler for the message `"AppStart"`.

```php
<?php
class recordController extends \exc\controller\viewController {
	public function onAppStart(){
		//my code here...
	}
}
```

When interacting with the browser we use **action messages**. In EXC the concept of an **action** represents a request or interaction with your front-end running in your browser or an API call.

For example if a link to show a record will use the action "showRecord" then we implement the message handler as follows:

```php
<?php
class recordController extends \exc\controller\viewController {
	public function onAction_showRecord(){
		//my code here...
	}
}
```

An **action message** is just like any other message, except that message name starts with "action_".

To get any form values (POST/GET) send by the browser we use the [client](./doc_server_client.md). For example:

```php
<?php
class recordController extends \exc\controller\viewController {
	public function onAction_showRecord(){
		$client = \exc\client::instance();
		$id = $client->values['id']
	}
}
```

## Loading a controller ##

When and how a controller is loaded and made available depends on your application logic. This section will cover some scenarios and use cases and how to load a controller on each case.

### App Controller ###

The `appController` is always loaded automatically.

EXC will look for your `appController` in a file named `controller.app.php` in your `src` folder or your application folder. You may also place the class in your startup php file or include it at some point before your start up file calls `\exc\bootloader::run()`.

For more details about your `appController` read this [documentation](./doc_server_appcontroller.md).

### Action Controllers ###

We call an action controller, a controller meant to handle a particular set of front-end actions. For example a controller that handles the login logic in the class `loginController`.

An action request is send by a browser or client using a URL. An action URL may specify a particular controller that is receiving the action, for example a URL my specify the action `"login.show"`, in which case the controller is the `loginController` and the message is the action `"action_show"`.

When a controller is specified in the request URL, EXC will automatically load the `loginController`. For EXC to be able to find and load your controller it must be placed in the same location as your `controller.app.php` file.


### Utility or Helper Controllers ###

A controller that is meant to hold shared code or implement helper functionality can be loaded manually or using your [config options](./doc_server_config.md) in `appController`.

When you load a controller manually you have to make it available to the app:

```php
<?php
$myController = new \navigationController;
$myController->initialize(); //call your initialize if needed!

\exc\app::registerController($myController);
```



## Using a controller ##

A controller is an [event emitter](./doc_server_events.md), meaning that it can publish messages for events and others can register [handlers](./doc_server_events.md) for its events.

Every controller inherits from the class `\exc\core\controller` and `\exc\core\base`.

| Function | Description |
| -- | -- |
| `$controller->on($eventName, $callbackMethod, $cookie)` | Register a callback for a given event. |
| `$controller->publish($eventName, $paramsArray)` | Publishes an event. |
| `$controller->off($eventName, $callbackMethod)` | Removes a callback. |

| `\exc\core\controller::isControllerInstance($object)` | Returns true if the `$object` is a controller instance. |
| `\exc\core\controller::registerHandlers($targetController, $handlerObject);` | Register callbacks on the `$targetController` for each function on `$handlerObject` that its name matches the event handler signature. |





# Class viewController #

A `viewController` is a specialized controller for use in an application where we create and build an interactive UI.


| Instance Methods | Description |
| -- | -- |
| `appController()` | returns the global instance of the `appController`. |


# Class \exc\app #

| Class Methods | Description |
| -- | -- |
| `controller()` | Gets the appController instance. |
| `getController($controllerName)` | Get instance of an application controller. |
| `registerController($className)` | Registers an instance of `$className` as an application controller. |
| `registerController($className, $filePath)` | Loads the file pointed by `$filePath` then registers an instance of `$className` as an application controller. |
| `registerController($controllerObject)` | Registers the object instance `$controllerObject` as an application controller. |
| `client()` | Returns the `\exc\client` instance. |
