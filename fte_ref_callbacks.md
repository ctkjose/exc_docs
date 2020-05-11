[Frontend](./fte_index.md) {menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Callbacks #

Across the documentation you will see the use of callbacks.

When a callback is accepted you can use a traditional `function` or a `closure`.

In we can use an array to reference a callback from object. This is similar to the syntax of PHP's [call_user_func_array](https://php.net/manual/en/function.call-user-func-array.php). The array is a simple array with two elements. The first element is the object and the second element is a function or a string with the name of a function.

```js
var callback = [MyObject, "doThis"];
```

The function element is a string, EXC will look for a function on the object with that name if one is found it is executed. If the function element is an actual function it will be executed with the `this` scope set to the object on the first element.


## Helper Functions ##

Check if a value is callable.
```js
	var bool = core.isCallable(fn);
```
The most useful function is `core.getCallback()`, it gets a function from a value that may be a callable. This function returns `undefined` if the value is not callable or it can not be converted to a callable function. This function is useful to handle strings with controller actions or messages like `"[aMessage]"` or `"[aController.message]"`.

```js
 	var fn = core.getCallback(aValue);
 	if(fn) fn(arg1, arg2);
```

Executes a callback with passed arguments

```js
	var returnValue = core.callback(fn, arg1, arg2);
```

Executes a callback with an array of arguments

```js
	var args = [arg1, arg2];
	var returnValue = core.callbackWithArguments(fn, args);
```
