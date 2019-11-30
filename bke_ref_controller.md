[Core Classes]() {menu:topics}
[app](./bke_ref_app.md) {menu:topics}
[appController](./bke_ref_appcontroller.md) {menu:topics}
[view](./bke_ref_view.md) {menu:topics}
[controller](./bke_ref_controller.md) {menu:topics}

**EXC** | DEV | [Documentation](./doc_index.md) <BR>

# Class controller #

The class `\exc\controller` provides the controller behavior. Every controller must inherit from this class or one of its sub classes.


# Functions #

## Function on() ##

Registers a callback to given message.

`$controller->on(string $messageName, callable $callback, mixed $cookie)`

### Parameters ###

**$messageName** (string)(required) The name of the message to add the callback to.

**$callback** (callable)(required) The function to be executed when the message is published.

**$cookie** (mixed)(optional) An additional parameter that will be passed to the callback function when executed.

## Function publish() ##

Publishes a message with the passed parameters.

`$controller->publish(string $messageName, array $params)`

### Parameters ###

**$messageName** (string)(required) The name of the message to publish.

**$params** (array)(optional) An array with the parameters used to call the functions registered to this message.

## Function off() ##

Remove a callback registered to given message.

`$controller->off(string $messageName, callable $callback)`

### Parameters ###

**$messageName** (string)(required) The name of the message with the passed callback.

**$callback** (callable)(required) The function to be removed.


## Function isControllerInstance() ##

Returns true if the passed object is an instance of a controller.

`$bool = \exc\controller::isControllerInstance(object $any)`

### Parameters ###

**$any** (object)(required) The object to test.
