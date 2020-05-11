[Frontend](./fte_index.md) {menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Trait Emitter #

The trait **Emitter** adds publisher-subscriber functionality to objects. Traits are decorators functions that add functionality to an existing object.

Emitters are used to implement event-based patterns and support decoupling of components and  facilitate asynchronous operations.


Dependencies: CORE.<br>
Implements: DISPOSABLE, THENABLE.<br>

## Creating an Emitter ## {#create}

Use the `emitter()` function to add the publisher-subscriber functionality to an object.

```js

	exc.traits.emitter(myObj);
```

## Publishing an event ## {#publish}

Use `emit(eventName, args...)` to emit an event.

```js

myObj.emit("update", "Joe");

```

The function `emit()` takes the following parameters:

| Parameter | Description |
| -- | -- |
| event | A string representing the event name to emit. |
| ... | Additional parameters passed to the event as arguments |

## Subscriber ## {#subscriber}

A publisher is an event emitter, for each event we can subscribe handlers to be execute when the event is published.

We use the `obj.on(eventName, callable)` method to add an event handler.

```js
var myObj = {
	fname: "Jose",
	lname: "Cuevas",
	type: 100,
};

exc.traits.emitter(myObj);

myObj.on("update", function(v1){
	console.log("update called v1=%o", v1);
});

myObj.emit("update", "Joe");
```

The signature of the event handler function passed to `on()` must match the parameters that the event will have. In our previous example the event handler takes one argument.

When we register our event handler with the method `on()` we create a `subscription`.

```js
var subscription = myObj.on("update", function(v1){
	console.log("update called v1=%o", v1);
});
```
In most cases we can think of the `subscription` as the representation of the event handler and its execution.

We use the `subscription.dispose()` method to remove our event handlers.

We can subscribe multiple event handlers at once passing a plain object.

```js
var subscription = myObj.on({
	update: function(v1){
		console.log("update called v1=%o", v1);
		return v1.toUpperCase();
	},
	error: function(msg){
		console.log("error=%s", msg);
	},
});
```

The function `on()` takes the following parameters:

| Parameter | Description |
| -- | -- |
| event | A string representing the event name to subscribe for. You may pass a plain object for multiple subscriptions. |
| callback | A [callable](./fte_ref_callbacks.md) called when the event specified is received. |
| opOnTop | An optional boolean indicating that subscribed callback will be added to the top of the current callback stack. |

Returns the object itself for chaining.

## Subscription ## {#subscription}

The property `subscription.publisher` is a reference to the original emitter object.

### Chaining a subscription ### {#chsubscription}

We can turn our `subscription` into a [thenable](./fte_ref_trait_thenable.md) object and use it to execute other functions in a chain using `subscription.then()`.

```js
var myObj = {
	fname: "Jose",
	lname: "Cuevas",
	type: 100,
};

exc.traits.emit(myObj);

//subscribe and make subscription thenable
var subscription = exc.traits.thenable(myObj.on("update", function(v1){
	console.log("update called v1=%o", v1);
	return v1.toUpperCase();
}));

//using thenable...
subscription.then(function(v1){
	console.log("then1 called v1=%o", v1);
	return "HELLO " + v1;
});

myObj.emit("update", "Joe");
```

Similar to a promise `subscription.then()` takes an callable that is executed after the event handler is processed.

The `then()` function returns a new [thenable](./fte_ref_trait_thenable.md) that we can use to chain more code using `then()`.

When chaining functions using `then()` the value returned from the previous call is passed to the next function in the chain.

```js
var myObj = {
	fname: "Jose",
	lname: "Cuevas",
	type: 100,
};

exc.traits.emitter(myObj);

exc.traits.thenable(myObj.on("update", function(v){
	console.log("update called v=%o", v);
	return v.toUpperCase();
})).then(function(v){
	console.log("then 1 called v=%o", v);
	return "HELLO " + v;
}).then(function(v1){
	console.log("then 2 called v=%o", v);
});

myObj.emit("update", "Joe")
```
## Onetime subscriptions ## {#once}

The function `once()` is just like `on()` but the subscription will be executed only once. When the subscription is fulfilled it will be disposed.

> ! With `once()` we can subscribe only to one event. Passing an object as the event name is not allowed.

The function `once()` takes the following parameters:

| Parameter | Description |
| -- | -- |
| event | A string representing the event name to subscribe for. |
| callback | A [callable](./fte_ref_callbacks.md) called when the event specified is received. |
| opOnTop | An optional boolean indicating that subscribed callback will be added to the top of the current callback stack. |

Returns a [subscription](#subscription) object.

## Using sticky events ## {#sevent}

With `emit()` when an event is published only existing subscribers will be invoked. A sticky event is a published event that will remain active for future subscribers.

In other words once a sticky event is emitted any subscription that comes after will be fulfilled and called immediately.

Sticky events are quite useful to represent a state in your application.

To publish a sticky event we use the special `sticky` namespace to call the `emit()` function.  

```js
//event was published
myObj.sticky.emit("userLoaded", {username:"joe", name:"Joe"});

//Later in your app lifecycle somebody subscribes to the event
//This subscriber will be executed instantly because the event was already published
myObj.once("userLoaded", function(user){
	console.log("We have an user %o", user);
});
```

The function `emit()` returns a [disposable](fte_ref_disposable.md) object. We use the method `dispose()` to remove the sticky event.

```js
//event was published
var disposable = myObj.sticky.emit("userLoaded", {username:"joe", name:"Joe"});

//somewhere in code...
myObj.once("userLoaded", function(user){
	console.log("I will be called %o", user);
});

//later user logged out...
disposable.dispose(); //revoke the userLoaded event

//somewhere afterwards...
myObj.once("userLoaded", function(user){
	console.log("I will NOT be called %o", user);
});
```

> Note: `publisher.sticky.remove(event)` can also be used to revoke a sticky event.

The function `publisher.sticky.emit()` is the same as a regular `emit()` and takes the following parameters:

| Parameter | Description |
| -- | -- |
| event | A string representing the event name to emit. |
| ... | Additional parameters passed to the event as arguments |

Returns `disposable` object.


## Pausing an emitter ## {#eventpause}

You can pause the delivery of events by calling `emitterTogglePause()`. When you pause the emitter all events triggered with `emit()` will be posponed until the pause is removed by calling `emitterTogglePause()` a second time. Once the pause is removed the emitter will deliver the events.


# Other emitter methods #

## Function subscribeHandlers() ##

Automatically subscribes methods in an object based on their name.

`emitter.subscribeHandlers(object)`

| Parameter | Description |
| -- | -- |
| **object** | The object with methods to subscribe. |

Methods that start with "on" are considered event handlers. For example a method named "onDoThis" is an event handler for the event "DoThis".

After calling this function the object will have a `dispose()` function which can be called to remove the subscriptions.


## Function dispose() ##

This is a teardown method that releases the emitter data.

`emitter.dispose()`

The dispose functions is added in top of any existing one.
