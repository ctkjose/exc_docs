**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Value Observers #

Part of **EXC JS Client**<BR>
Dependencies: CORE, MODEL.<br>

## Observable Collection ## {#observable}

Is an object used to store a collection of key-value pairs that are observable.

The function `exc.ds.observable(aPlainObj)` creates an observable object.
Used to store a set of related items in a larger structure of key-value pairs that is observable.

```js
var data = {
	name: "Jose",
	lname: "Cuevas",
	code: 50
};

var observable = exc.ds.observable(data);
```

### Observing for key changes ###

To observe a key we use `observable.observeKey(keyName, fnCallback)`.

```js
var myObserver = function(value, key, obj){
	console.log("The Key [%s] changed to ", key, value);
};

observable.observeKey("name", myObserver);

//lets test our observer
observable.name = "Joe";
```

Since the observable is an event emitter using the [emitter](./fte_ref_trait_publisher.md) trait we can take advantage of its features.

When calling `observeKey()` a `subscription` is returned. Use the `subscription.dispose()` to stop observing a key change.

```js

var subscription = observable.observeKey("name", myObserver);
subscription.dispose();
```

An observable also emits the event `"change"` to indicate that some key in the object changed.

```js
var observable = exc.ds.observable({
	name: "Jose",
	lname: "Cuevas",
	code: 50
});

observable.on("change", function(obj, key){
	console.log("observable changed");
	console.log("Key %s=%o", key, obj[key]);
});
```

We can use a `subscription` to chain multiple functions when a key changes.

```js

observable.observeKey("name", function(value, key, obj){
	return value.toUpperCase();
}).then(function(value){
	console.log("Value is now %s", value);
});
```
### Adding a new key ###

To add an observable key we use `value(key, value)`.

### Properties and Methods ###

Other useful methods and properties and functions of an `observable`:

| Member | Description |
| -- | -- |
| keys | **Readonly Property** Returns an array with the keys in the collection. |
| hasKey(key) | Returns true if the key in `key` is part of the collection. |
| value(key) | Returns the value of a key in the collection. If not present `undefined` is returned. |
| value(key, value) | Sets the value of a key in the collection. If the key doesn't exists it will be created. |
| clone() | Returns a copy of the observable collection. |
| asPrimitive() | Returns a plain object with a property for each corresponding key in the collection. |
| serialize() | Returns a json string representing the keys in the collection. |


# DataSource # {#ds}

A `datasource` represents data from a source that can be remote (ex: API), or a local object.

The datasource holds one or multiple records, where each record represented as row in array-like manner.


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
## Members and Properties ##

| Key | Description |
| -- | -- | -- |
| length | Returns the number of rows in the dataset. |
| push() | Adds one or more elements to the end of an array and returns the new length of the dataset. |
| forEach(fnCallback(row)) | Calls a function for each row in the dataset. |
| slice(begin, end) | Extracts a section of the dataset and returns a new array. |
| item() | Returns the value of the current item when iterating over the datasource. |
| row(index) | Returns the record at a particular index on the datasource. |
| row(index, value) | Replaces the record at a particular index on the datasource. |
| rewind() | Resets the iteration state when using `next()` or `previous()`. |
| next() | Moves the datasource to point to the next available record. It returns `true` if it was able to move else it will return `false`. Use the `current` property to access the record. |
| previous() | Moves the datasource to point to the previous available item. It returns `true` if it was able to move else it will return `false`. Use the `current` property to access the record. |
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
