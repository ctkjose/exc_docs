[Frontend](./fte_index.md) {menu:}
**EXC** | DEV | [Documentation](./doc_index.md)

# Chain #

The helper `core.chain` is a function that bundles other functions to be executed sequentially. In short you can think of `chain` as a special function that runs other functions.

A Chain is extremely useful to ensure execution order in asynchronous or callback-heavy code.

In EXC we use `chain` to represent **actions**.


# Using a Chain #

`core.chain()` is a function that takes any number of functions as arguments and returns a function. We call the return function a "chain".

```js

var actionShowRecord = core.chain(
	function(chain){
		console.log("Getting Data....");

	},
	function(chain){
		console.log("Getting Data....");

	},

);
```

We execute the **chain** by either running the chain function or calling its `exec()` function.

```js

var aSelector = ".empForm";

//lets execute our chain and pass one parameter
actionShowRecord(aSelector);
//actionShowRecord.exec(aSelector); //same as above

```

We use our `actionShowRecord` chain like any other function.

A function added to a **chain** receives the `chain` itself as its first parameter, then: the first function on the chain gets any parameters that it received when the chain was executed, subsequent functions on the chain get the return value of the previous function as a parameter

Any value returned from a function is kept in the member array `returnValues` of the chain.

```js

var actionShowRecord = core.chain(
	function(chain, selector){
		console.log("Getting Data....");
		var category = 5;
		return category;
	},
	function(chain, category){
		console.log("Got category=" . category);
	},
);
```

When a member function returns a [Promise](.fte_ref_core_promise.md) execution of the chain will continue when the promise is resolved (then is executed).



## Helper methods ##


`chain.push(function)`

Use `push()` to add new function to the stack of a chain. This function takes a function or a [callable](./fte_ref_callbacks.md) array.


`chain.next()`

Use `next()` to manually move the stack to the next function in the chain. This function is handy when the code in your chain uses a callback, just set the callback to this function.

"chainable"
