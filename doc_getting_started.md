**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

## Getting Started ##

This guide applies to EXC Server Side Framework or SSF.

We will create a basic sample employee application to learn the basics of EXC.  Lets create a folder named `employee`  for our app.


## Structure of your app ##

EXC does not have a particular folder structure. We do recommend a structure that allows to build more secure apps for example:

```
./
└── myapp/
    ├── src/
	|   ├── controller.app.php
	|	└── controller.login.php
    ├── css/
    ├── js/
	└── index.php
```

In this example we place the php code inside a `src` folder where it can be jailed away from the public using a simple `.htaccess` file under Apache. See our [.htaccess](./doc_server_htaccess.md) recommendations to secure your app.

## Your startup script ##

Your app needs a startup php file that loads EXC and handles the incoming requests. Usually this is your `index.php` or a `app.php` file. In reality it can have any name. For the purpose of this guide we will use the file `index.php`.

In your startup file we add the following code
```PHP
<?PHP
require_once('../exc/exc.php'); //include EXC

\exc\bootloader::run();  //load EXC
```

The line `require_once('../exc/exc.php')` includes EXC. Make sure you use the correct path to the "exc.php" file. In this example the file is located in a folder named `exc`

The code `\exc\bootloader::run();` is what starts EXC and runs your application.


## The application controller ##

Once EXC starts the lifecycle of your app is managed by the [Application Controller](./bke_ref_appcontroller.md). The application controller is explained in more detail [here](./bke_ref_appcontroller.md).

Lets create a file `controller.app.php` and place in your `src` folder. In this file we will add our `appController` class like this one:

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
	public function willDispatchAction($actionName){
		//about to execute an action
		//we can change the action by returning a different action name here
	}
}
?>
```

### Configure your app ###

The function `config()` in your `appController` is a message handler that will be called to configure your application.
The configuration options are explained in detail [here](./doc_server_config.md).

Lets add some configuration options.

```php
<?php
class appController extends \exc\controller\appController {
	public function config(){
		$options = [
			'uid' => "ACMEEMP"; //our app identifier, every app needs an identifier...
		];

		return $options;
	}
}
?>
```
In our config handler we return an array of options. In this example we identify our application by assigning a `uid`.

### Your controllers ###

The bread and butter of the your app are controllers. A traditional UI based application uses controllers of type `viewController`, services and CLI applications use the `processController`. You will create instances of one of these to implement your app.

Lets create an instance of `viewController` for an employee record, our controller will be named "record".

Controllers must be created in a file by themselves,  we create a file named `controller.record.php` in our `src` folder.

Create an instance of `viewController` like this:

```php
class recordController extends \exc\controller\viewController {
	public function initialize(){
		//this is called when EXC creates the instance of your controller
	}
	public function onAction_main(){
		//this is our default action
	}
	public function onAction_show(){
		//show our employee record...
	}
	public function unhandled($msg){
		//message not implemented
		error_log('[ACME EMPLOYEE APP] MESSAGE [' . $msg . '] NOT IMPLEMENTED');
	}
}
```


The main job of your controller is to responde to **events**. An event may be an **action** from your front-end or you may get a **message** from the framework itself.



Any controller can listen to an "event" by creating an event function. An event function is simply a function named `on` + `message_name`.



For example when the app is ready an event `appInit` will be published, to handle the `appInit` event we just add the function `onAppInit` to our controller.

```php
	public function onAppInit(){
		//this is a message function
		//this function will be executed when an ```appInit``` message is published
	}
```

See the section on [events](./doc_server_events.md) to learn more on the different messages that EXC will publish and how to publish your own.

In the other hand the `client` (frontend) can request an action from your backend controller, lets  said the action `showRecord` to show an employee record. TO handle this action request we will simply add this event function:
```php
	public function onShowRecord(){
		//my code here...
	}
```

> **NOTE** An action name is case insensitive, therefor `showRecord` and `showrecord` are the same.

### Building the user interface ###
The user interface is build using views and EXC's built-in templating functionality.

A view is used to build your user interface. In EXC we can manipulate views from the server side prior to serving the content to the browser or dynamically in the client side.

A "view" is a php file named `view.myname.php`, where `myname` is the actual name of your view.

See [Views Documentation](./doc_server_views.md) to learn more about views.

You place your views in a folder named `views` in your app folder.

A view file contains html in combination with the templeting functionality.
