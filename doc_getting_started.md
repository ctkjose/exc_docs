**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

## Getting Started ##

This guide applies to EXC Server Side Framework or SSF.

We will create a basic sample employee application to learn the basics of EXC.  Lets create a folder named `employee`  for our app.

### Your app definition ###

Every app needs an `app.php` file which tells EXC about your app. Inside `app.php` we use the `$options` array to configure our app.

```php
<?php

$options['uid'] = "ACMEEMP"; //our app identifier, every app needs an identifier...

//add some controllers controllers
$options['controllers']=[
	"record" => "./controller.record.php",
	"findEmployeeController" => "./controller.findEmployee.php",
];

?>
```

### Your controllers ###

The bread and butter of the your app are controllers. A traditional UI based application uses controllers of type `viewController`, services and CLI applications use the `processController`. You will create instances of one of these to implement your app.

Lets create an instance of `viewController` for an employee record, our controller will be named "record".

Controllers must be created in a file by themselves,  we create a file named `controller.record.php` in our app folder.

Create an instance of `viewController` like this:

```php
class recordController extends \exc\controller\viewController {
	public function initialize(){
		//this is called when EXC creates the instance of your controller
	}
	public function event_action_main(){
		//this is our default action
	}
	public function event_action_show(){
		//show our employee record...
	}
	public function event_appInit(){
		//this is a message function
		//this function will be executed when an ```app_ready``` message is published
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
