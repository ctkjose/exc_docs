**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Server-side Controllers #
*This topic applies to the BackEnd framework.*

The bread and butter of the backend are controllers, they manage the lifecycle of your app.

There are different types of controllers each with additional functionality depending on your type of application or tasks. You will create instances of these controllers to implement your app.

The `appController` is the orchestrator of your application lifecycle. Every application must a have an instance of an `appController`.

Additionally you may add instances of `viewController` for UI based applications and `processController` for services and cli applications.

A controller is an [event emitter](./doc_server_events.md), meaning that it can publish events and others can register [handlers](./doc_server_events.md) for its events.

Every controller inherits from the class `\exc\core\controller` and `\exc\core\base`.

| Function | Description |
| -- | -- |
| `$controller->on($eventName, $callbackMethod, $cookie)` | Register a callback for a given event. |
| `$controller->publish($eventName, $paramsArray)` | Publishes an event. |
| `$controller->off($eventName, $callbackMethod)` | Removes a callback. |
| `\exc\core\controller::isControllerInstance($object)` | Returns true if the `$object` is a controller instance. |
| `\exc\core\controller::registerHandlers($targetController, $handlerObject);` | Register callbacks on the `$targetController` for each function on `$handlerObject` that its name matches the event handler signature. |


# Controller appController #

Every interaction or request to your backend is orchestrated by the `appController`, this is the main controller loaded by EXC.

For most scenarios in EXC you will make your own `appController` class to handle your app's logic.

EXC will look for your class in a file named `controller.app.php`.  This file defines a class named `appController` that extends the built in controller `\exc\controller\appController`. For example:

```php
<?php
class appController extends \exc\controller\appController {
	public function config(){
		//config our app...
	}
	public function initialize(){
		error_log("--- @appController->initialize() ---");
	}
	public function unhandled($msg){
		//called when a message is not implemented
		error_log("@appController->unhandled ---- $msg");
	}
}
?>
```

> If you don't have a `controller.app.php` EXC will create a generic instance of the `appController` automatically.

There are various ways to get a reference of the `appController`.

```PHP
<?php
$app = \exc\app::controller();
```

```PHP
<?php
$app = \exc\controller\appController::instance();
```

If you have a class that inherits from `viewController` or `processController` you can use:
```PHP
<?php
$app = $myObject->appController();
```
or inside a `viewController` or `processController` as:
```PHP
<?php
$app = $this->appController();
```


The `appController` is used to publish [events](./doc_server_events.md) related to the execution of your app. In general we add event handlers to the `appController`.

The `appController` may have delegate controllers, which actually implement the behavior or execute your applications logic, these are called **application controllers**.

The **application controllers** are instances of `viewController` or `processController` that you create. See **Adding your own controllers** in this document for more information.

When your controller is added as an application controller EXC will automatically register event handlers for events published by the `appController`.

You can manually register for an `appController` event by using it's `on()` function.

```php
<?php
$app = \exc\controller\appController::instance(); //get an instance of the app controller

$app->on( 'appStart', $callback ); //register for the event appStart
```

# Adding your own controllers #

You create a controller by creating a class that extends one of the existing controller types. For example:

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

The name of the class always ends with `"Controller"`. In the above example the name is `"record"` and the corresponding class name is `recordController`.

Each controller must be created in a file by themself and placed in your app folder.  The file name follows this convention `controller.name.php`, for example: `controller.record.php` .

A controller is loaded by adding it to your [app.php](./doc_server_config.md) configuration or manually by using:
```php
<?php
$myController = new \recordController;
$myController->initialize(); //call your initialize if needed!

\exc\app::registerController($myController);
```

# Class appController #

Inherits from the class `\exc\core\controller` and `\exc\core\base`.

| Instance Methods | Description |
| -- | -- |
| `setOption($key, $value)` | Sets an application option. |
| `getDefaultView()` | Returns the view instance designated as default view. Returns null if not set. |
| `client()` | Returns the `\exc\client` instance. |
| `header($name, $value)` | Sets an HTTP header. |
| `end()` | Terminates the application execution. |
| `abort()` | Aborts the application execution. |
| `sendJSON($data)` | Sends a JSON payload to the front-end. The parameter `$data` may be a hash of key-value pairs or a json encoded string. |
| `sendJS($code)` | Sends valid a string with javascript code to the front-end. |
| `sendView($aView)` | Sends the contents of a `view` to the front-end. |
| `write($data)` | Appends the contents of `$data` to the output buffer. |
| `commit($payload)` | Sends a payload to the front-end. |
| `sendDownloadWithData($mime, $data, $filename=null)` | Sends `$data` as a file attachment. |

| Class Methods | Description |
| -- | -- |
| `instance()` | returns the global instance of the `appController`. |


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
