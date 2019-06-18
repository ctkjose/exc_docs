**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Value Observers #

Part of **EXC JS Client**<BR>
Dependencies: CORE.<br>

## ValueCollection ##
Used to store a set of related items in a larger structure of key-value pairs that is observable.

Create a ValueCollection using `exc.ds.makeValueCollection(aPlainObj)`.

```js
var data = {
	name: "Jose",
	lname: "Cuevas",
	code: 50
};

var collection = exc.ds.makeValueCollection(a);
```

You can register an observer to be notified when a key in the collection is modified.

Use `valueCollection.addObserverForKey(keyName, fnCallback, [cookie])` to register an observer and `valueCollection.removeObserverForKey(keyName, fnCallback)` to remove the observer.


```js
var myObserver = function(aCollection, keyName){
	console.log("The Key [%s] changed to ", keyName, aCollection[keyName]);
};

collection.addObserverForKey("name", myObserver);

//lets test our observer
collection.name = "Joe";
```

To be notified when any key in the collection is changed use `valueCollection.subscribe(fnCallback, [cookie])` and `valueCollection.unsubscribe(fnCallback)` to remove the observer.

Other useful methods and properties of a `valueCollection`:

| Property | Description |
| -- | -- |
| keys | **Readonly Property** Returns an array with the keys in the collection. |
| hasKey(keyName) | This function returns true if the key in `keyName` is part of the collection. |
| appendKey(keyName, initialValue) | This function adds a new key to the collection. |
| assign(aPlainObject) | This function sets the values of keys in this collection to the values of properties in `aPlainObject` with the same name as the key. |
| asPrimitive() | Returns a plain object with a property for each corresponding key in the collection. |
| serialize() | Returns a json string representing the keys in the collection. |


# ValueStore #

A ValueStore is an observable value.

Create a ValueStore using `exc.ds.makeValueStore(value, [ops])`.

```javascript
var vs = exc.ds.makeValueStore(value, ops);
```

## Setting and getting a value. ##
```javascript
v = vs.value;

//setting its value will notify its observers
vs.value = newValue;
```

Use `valueStore.observe()` and `valueStore.removeObserver()` to add or remove an observer.

```javascript

//Use a plain function for our callback
//You can also use any of the callback modalities supported by EXC.
var myValueChangedCallback = function(value, cookie){
	console.log("New value %o", value);
	cookie.owner.value = value; //update my UI
}


//cookie is optional
//var cookie = recordID
var cookie = {owner: domElement};
vs.observe(myValueChangedCallback, cookie);
```
Other useful methods and properties of a `valueStore`:

| Property | Description |
| -- | -- |
| observe(fnCallback, cookie) | This function add an observer. The optional parameter `cookie` is a value that will be passed to the callback when a change notification occurs. |
| removeObserver(fnCallback) | Removes an observer using this callback. The callback `fnCallback` must be the same instance used to register the observer. |
| getValue() | Returns the primitive value. |
| setValue(aNewValue) | Sets the primitive value. |


# DataSource #

A data source represents data from a source that can be remote (ex: API), or a local object.

The `dataSource` object obtains its data from one of the supported **sources**.

To create a datasource we use `exc.ds.create(options)`.
```js
var ds = exc.ds.create({
	type: "rest",
	method: "post",
	url:"http://localhost/app1/data.js",
});
```
The `create(options)` method expects a plain object in the parameter `options`. The `options` object has particular values used by the selected source.

| Key | Description | Values |
| -- | -- | -- |
| type | **Required** String indicating the source type. | "rest" |
| processData | Optional function that will be called to process data received. This function must return an array or object. | function(data) |

To indicate the source of a datasource we use the property `type` in our `options` object. In the previous example we use the source type `"rest"`.
As the name implies the source **rest** is a basic source of data used to fetch data using a REST request. This source uses for its data the **JSON** returned by the request ( either as one plain object or an array of plain objects) (*for more details see **JSON API** in this document*).

A source of type `"rest"` uses the `url` option and the `method` option. The `method` may be set to `get`, `post` or another valid HTTP method.

The URL of our rest service my use parameters. For example:

```js
var ds = exc.ds.create({
		type: "rest",
		method: "post",
		url:"http://localhost/exc_core/app1/testAPI.php?id={id}",
});
```

In this second example we have the URL parameter `id`. A URL parameter lets you compose a dynamic URL.


Use the `params()` function to set parameters in a dynamic URL or additional parameters.  For example:

```js
ds.params('id', 205);
```

you may set multiple parameters at once:

```js
ds.params('id', recordID, 'location', locationID); //multiple keys
```



The `dataSource.load()` function is used to load the datasource, which in our example will be a **rest** request as configured in our `create()`.

```js
ds.load().then(function(ds){
	console.log("ds is ready...");
	console.log(ds);
});
```

A `dataSource.load()` returns an instance of `core.promise` which is EXC's stand in replacement for a native **promise** (See promises).

```js
ds.params('id', 205).load().then(function(ds){
	while(ds.next()){
		var record = ds.item();
	}
});
```


| Key | Description |
| -- | -- | -- |
| item() | Returns the value of the current item when iterating over the datasource. |
| item(index) | Returns the value of a particular item on the datasource. |
| forEach(fnCallback(item)) | Executes the provided function once for each item in the datasource. The callback will receive the current item being processed. |
| rewind() | Resets the iteration state when using `next()` or `previous()`. |
| next() | Moves the datasource to point to the next available item. It returns `true` if it was able to move else it will return `false`. Use `item()` to get the current item. |
| previous() | Moves the datasource to point to the previous available item. It returns `true` if it was able to move else it will return `false`. Use `item()` to get the current item. |
| hasKey(keyName) | Checks if the current item has a property with the name provided in `keyName`. |
| where(opName, opValue [, ...])<br><br>where(plainObject) | Use to set `load()` parameters. |



# Using a DataSource with JSON API #

API support in EXC is based on a very simplistic format that is self contained.

EXC expects a JSON response to follow a basic set of rules. These rules are inspired by specifications like [JSend](https://github.com/omniti-labs/jsend), [JSON API](https://jsonapi.org/format/) and [HAL](http://stateless.co/hal_specification.html). We also looked at other standards/specifications ([Swagger/JSON-SCHEMA](http://json-schema.org), [Open-API](https://github.com/OAI/OpenAPI-Specification) , [OASIS ODATA](http://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940655) ) and found some interesting concepts, yet we found them a bit too complicated for the spirit of EXC.


An API response is a plain JSON object with some special keys.

| Key | Description | Values |
| -- | -- | -- |
| status | **Required** A string property indicating the state of this request.<br><br>A successful call will have the value `"success"`. | `success`<br>`fail` |
| data | A plain object or an array of plain objects. <br><br>This field is required for a successful request.| `{...}`<br>`[...]` |
| paginate | **Optional** Boolean indicating that results are paged.| true<br>false |
| pageSize | **Optional** Integer indicating the number of items in a set, when `paginate` is true. | true<br>false |

EXC supports plain JSON and JSON based API.

### Error Responses ###
On error an API sets the `status` property to `fail` and optionally may send an `error` object.

The `error` object contains the following keys.

| Key | Description |
| -- | -- | -- |
| code | An application-specific error code as a string value. |
| detail | A string with a human-readable explanation or message. |

On error the `data` property should be omitted or if included it must have a value of `null`.
