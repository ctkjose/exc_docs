**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

## Building the UI ##
*This topic applies to both the Backend and Front-End frameworks.*

EXC applications are in essence single page javascript applications ([SPA](https://en.wikipedia.org/wiki/Single-page_application)).

The UI lifecycle of our SPA starts with EXC sending a landing page, in EXC we call this initial page our **default view**. The default view holds the core UI layouts and contents of our app.

EXC will publish the [event](./doc_server_events.md) `appStart` to let you know that we need to build and send our landing page.

Subsequent operations or user interactions will dynamically display additional views, dialogs, or modify the app contents as required.


## Using views ##

On the server side we use **[views](./doc_server_view.md)** to manage fragments of HTML used to build our UI.

In PHP we can compose, modify or create dynamic content that we can send to our front-end.

A **view** is a php file named `view.myname.php`, where `myname` is the actual name of your view.

> **NOTE:** The view can be an HTML file like  `view.myname.html`.

You place your view files in a folder named `views` in your app folder. This is where EXC would look for them by default.

To learn more about creating a view see the [view documentation](./doc_server_view.md).

## The default view ##

The landing page of your app is contained in your **default view**. This is the web page that is serve as the main application. It has the logic to do things like show a login, register a user, show your initial content, etc.

You load your default view using the method `\exc\views\view::default(name)`:

```PHP
<?php
$view = \exc\views\view::default("default");
```

> **NOTE:** You can omit the `$name` argument if your default view is named `"view.default.php"` or `"view.default.html"`.

You may also use the method `createDefaultView(name)`:
```PHP
<?php
$view = \exc\ui\views\manager::createDefaultView("myname");
```

Once a default view is set you can obtain an instance of your the default view with:
```PHP
<?php
$view = \exc\ui\views\manager::getDefaultView();
```

## Adding views to your output ##

In the backend there are various ways to add a `view` other than your default view.

The simplest one is to use a **view directive** inside the html of your default view, like this:

```HTML
	{{#view myViewName}}
```
In your code you can use the function `$client->addView()`, this is useful when you have a more elaborated logic to decide what view to include.
```php
<?php
$viewLogin = \exc\views\manager::getView("login"); //get a view
$client->addView("loginView", $viewLogin); //add to the front end
```

## UI Library ##

The html contents of your default view defines your application UI. You can use your own html and css or use a UI library like **bootstrap**.

While nothing stops you form implementing your own UI or using something like bootstrap, the beauty of EXC is that it already comes with a full UI library that we call **Space**.

The **Space** UI defines the basic layout and look and feel of your app. **Space** is a modern mobile first UI that is suitable for most use cases. **Space** is build with [SASS](https://sass-lang.com) and a simple structure that makes it easy to modify.

In addition all of the UI components (widgets, buttons, etc) included are ready to be use and look beautiful out of the box.

Given that EXC is build for simplicity all the neat things that EXC does out of the box are meant for **Space** and the included components. Of course the code in EXC (css, etc) is quite modular if you want to venture using another UI or customizing **Space**.



## The AppStart event ##

The `AppStart` is triggered to notify you that we need to build and send our initial page.

You add an [event](./doc_server_events.md) handler for `AppStart` in your [application controller](./doc_server_controllers.md). For example:

```php
<?php
class mainController extends \exc\controller\viewController {

	public function onAppStart(){
		$view = \exc\views\view::default(); //add our default view

		//modify the title...
		$view->title->set("Welcome");

		$client = \exc\client::instance();
		$client->session("user", ""); //reset the user...

		//add an additional view
		$viewLogin = \exc\views\manager::getView("login");
		//add the view to the manifest
		$client->addView("loginView", $viewLogin);

		//add a css
		$client->addCSSFile("assets://css/record.css");
	}
}
?>
```

In addition of setting up our default view, notice that we use the [$client](./doc_server_client.md) in our `AppStart` to add additional content to our front-end.

In the above example we sent a secondary view named `"loginView"`. A view send by `$client` is not actually displayed, but instead is made available to your front-end. The logic of your front-end code decides when to actually display this view.  (Actually we lied! your backend can display a secondary view like the `loginView` using [client interactions](./doc_server_client.md), but thats a more advance topic.)

We can also use the `$client` to add css and javascript files.

## The stage and managing views in your front-end ##

While the first stage of building your UI is to define the landing page and the different UI fragments that will be used in your app, further manipulations and interactions occurs on the front-end once your application is served to the user's browser.

Views that you either create in your backend or any other dynamic content gets served to the front-end.

The [stage](./doc_client_stage.md) is responsible for displaying those views and handling user navigation.

A full discussion of the stage is available [here](./doc_client_stage.md).
