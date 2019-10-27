[Core Classes]() {:menu}
[app](./bke_ref_app.md) {:menu}
[appController](./bke_ref_appcontroller.md) {:menu}
[view](./bke_ref_view.md) {:menu}
[controller](./bke_ref_controller.md) {:menu}

**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>



# APP Class #

The class `app` is a class that serves as a central orchestrator and exposes many static functions to manage your app.

> ! **IMPORTANT** you do not create instances of the `app` class, instead you use its static functions.

# Functions #

## Function applyFilter() ##

Request filter hooks to be executed on a given value.

In EXC we use filter hooks (similar to Wordpress Filter Hooks) to allow other parts of an application or extension to modify data by means of callbacks.

`$value = \exc\app::applyFilter(string $tag, mixed $value)`

### Parameters ###

**$tag** (string)(required) The name of the filter requested.

**$callback** (mixed)(required) The value for which we request a filter to be applied.

## Function registerFilter() ##

Adds a callback to a given filter tag.

In EXC we use filter hooks (similar to Wordpress Filter Hooks) to allow other parts of an application or extension to modify data by means of callbacks.

`$value = \exc\app::registerFilter(string $tag, callable $callback, int $priority)`

### Parameters ###

**$tag** (string)(required) The name of the filter requested.

**$callback** (callable)(required) The callback function to be executed for the given filter tag.

**$priority** (int)(optional) Specify the order in which this callback will be applied. The smaller numbers will be executed first. The default value is 10.

## Function registerController() ##

Lets EXC knows of that a controller exits and makes it available. Messages will be broadcasted to this controller.

`\exc\app::registerController(mixed $class, string $path)`

### Parameters ###

**$class** (mixed)(required) A string with the name of the controller class or an instance of the controller.

**$path** (string)(optional) A string with a path to the php file with the controller class. When used $class must be the name of the class to load from this file. You may use EXC path shortcuts.

## Function client() ##

Returns the global `\exc\client` instance.

`$client = \exc\app::client()`
