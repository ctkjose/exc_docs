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


# Messages #

In EXC the app logic is orchestrated by means of [messages](./doc_server_events.md). The main job of a controller is to handle a given message or broadcast a message.

A particular message is identified by its name and the number of arguments it sends to a handler, that combination of name and parameters is the **message signature**.

When a message is broadcasted to all active controllers this is called **publishing a message**. In addition a controller maybe directed to execute a message or **perform a message**.

A message handler is a public function in a controller with a name and parameters that match a **message signature**. Messages are explained in detail [here](./doc_server_events.md).

For **published messages** a handler function has a name that starts with **"on"** , for example `onAppInit` is considered a handler for the message `"AppInit"`. We use the prefix "on" to identify functions as message handlers that EXC will automatically register.

```php
<?php
class recordController extends \exc\controller\viewController {
	public function onAppInit(){
		//my code here...
	}
}
```

> * TIP: In your application the `appController` is responsible for broadcasting messages. Since all controllers are [event emitters](./doc_server_events.md) you can use them to create your own message based logic which is great to decouple applications.


## Action messages ##

When interacting with the browser we use **action messages**. In EXC the concept of an **action** represents a request or interaction with your front-end running in your browser or an API call.

For example lets use an action named "showRecord" in a link, the link will become "./myapp/index.php?a=showRecord", in this link the `a` argument has the value of our action.

We can implement the message handler for our "showRecord" action as follows:

```php
<?php
class recordController extends \exc\controller\viewController {
	public function onAction_showRecord(){
		//my code here...
	}
}
```

You can see that an **action message** is just another message, except that message name starts with "action_".

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

## More on message handling functions ##

We learned that there are two ways to receive a message: a message may be broadcasted to all active controllers, this is called **publishing a message**; or a controller maybe directed to **perform a message**.

Publishing messages is a more generic approach to decoupling an application logics while having a controller perform a particular message is more specific to its job.

To understand the difference of **publish** and **perform** we need to talk about controller roles. Controllers may have a particular job or role in your application. When a controller has a particular role or job we call it a **delegate** controller.

For example a controller like `appController` has a specific job **"to be your application controller"** as such EXC can make some assumptions about its use.

The other particular job or role that is important for this topic is the **first responder**. When your back-end is handling a request that request is process by your **first responder**. By default your `appController` assumes the duties of a first responder, but any controller may become the first responder.

In many instances EXC will directly tell a delegate controller to **perform a message**. In which case that message CAN NOT be handle by any other controller, only the one getting the message can handle it.

> * All controller implement the trait `objectExtendable` which allows you to delegate particular messages to other objects or functions. See [Core](./doc_server_core.md) for more information.

In most cases the message is **published** which means that any registered controller may implement a message handler using a `on...()` function.

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
