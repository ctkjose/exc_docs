[Frontend](./fte_index.md) {menu}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Promise #

You already know that a promise is an object that allows us to make the asynchronous nature of JS more manageable.

In EXC we use the built-in [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/promise) as supported by a browser. Promises are used a lot and thus a [browser](http://kangax.github.io/compat-table/es2016plus/) with promise support is required.


In addition we have some extensions and helpers for the built-in promises.

# Deferred # {#deferred}

A deferred is promise proxy that allows you to fulfill the promise at a later time.

```js
var p = exc.deferred();

p.then(function(value){
	console.log("promise resolved...");
	console.log(value);
});

//later on, somewhere in your
p.resolve("Hello Jose");

```
A deferred is fulfilled by calling its `resolve(value)` or `reject(value)` methods.

A deferred object uses exactly the same `then()` and `catch()` methods of a Promise.



In EXC a promise is deferred (similar to what JQUERY and other libraries did before ES6 introduced promises)

# Creating a promise # {#creating}

> This is deprecated. EXC no longer support non ES6 browsers.

Since there are a lot of patterns to create a promise, lets start with some basic examples and get fancier from there.

```js

var p = exc.promise.create();

//lets do something boring asynchronously...
setTimeout(function(){
    p.resolve("Success!"); //Yay! Everything went well!
}, 250);

p.then(function(successMessage){
	//successMessage is whatever we passed in the resolve(...) function above.
  	//It doesn't have to be a string, but if it is only a succeed message, it probably will be.
   	console.log("Yay! " + successMessage);
});
```

The method `exc.promise.create()` is our factory function.


## Functions ##

### promise.reject() ### {#p-reject}

Rejects a promise and executes the callbacks set for failure with the given args.

```js
	promise.reject([args]);
```

When the promise is rejected, any callbacks added by deferred.then() are called.

### Parameters ###

***args*** (any)(optional) One or more arguments that will be passed to the fail callback.

### promise.resolve() ### {#p-resolve}

Resolve a promise and executes the callbacks set for success with the given args.

```js
	promise.resolve([args]);
```

When the promise is resolved, any callbacks added by deferred.then() called.

### Parameters ###

***args*** (any)(optional) One or more arguments that will be passed to the success callback.

### promise.then() ### {#p-resolve}

Sets callbacks for what to do when the promise is resolved either with success or failure. A new promise and executes the callbacks set for success with the given args.

```js
	promise.then(successFn, failureFn);
```

When the promise is resolved, any callbacks added by deferred.then() called.

### Parameters ###

***args*** (any)(optional) One or more arguments that will be passed to the success callback.
