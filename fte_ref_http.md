[Frontend](./fte_index.md) {menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# HTTP #

Use the HTTP object to make a request using http/https, like calling an API or loading resources from a server.

Dependencies: CORE.<br>


## Making a request ## {#makerequest}

Use the functions `http.get()` and `http.post()` to initiate a HTTP request.

```js
	var request = http.get(url);

	var request = http.get(url, data);

	var request = http.get(url, data, callback);

	var request = http.get(url, callback);
```
The only required argument is either a `url` or a plain object with options. (See "Setting more options".)

| Arguments | Description |
| -- | -- |
| url | String with a valid URL. A `url` parameter may be replaced with the `options` argument. |
| options | A plain object with configuration options. |
| data | **Optional** A URL encoded string or a plain object (key value pairs) |
| callback | **Optional** A function called when the request is completed. Is called for both, success and errors. See "Using the callback". |

### Setting more options ###

You can pass a plain object to the functions `http.get()` and `http.post()` to control and configure various options of a request.

This plain object replaces our `url` parameter and as such it must include the property `url` in your options object.

```js
	var options = {
    	url: "http://example.com/",
    	headers: {
        	'Content-Type': 'application/x-www-form-urlencoded',
    	}
	}

	var request = http.get(options);

	var request = http.get(options, data);

	var request = http.get(options, data, callback);

	var request = http.get(options, callback);
```

The following options are available.

| Option | Description |
| -- | -- |
| url | **Required** String with a valid URL. |
| headers | A plain object with headers. "POST" request set the `Content-Type` header to `application/x-www-form-urlencoded` by default. |
| opRunJS | A boolean that indicates if javascript code received with the `content-type` of `application/javascript` is executed automatically. Default is true. See "Javascript Payloads". |
| timeout | The number of milliseconds a request can take before automatically being terminated. The default value is 0, which means there is no timeout. |
| mimeType | Overrides the MIME type returned by the server. |
| withCredentials | A Boolean that indicates whether or not cross-site Access-Control requests should be made using credentials such as cookies, authorization headers or TLS client certificates. Setting withCredentials has no effect on same-site requests. (Source MDN) |

## Handling the response ## {#handleresp}

The HTTP request provides three ways to handle a response, using events, using a promise interface or by a more traditional callback.

## Using events ## {#useevents}

An http request implements the [emitter](./fte_ref_trait_publisher.md) trait.

```js
	var request = http.post(url, data);
	request.on( "done", function(response){
		console.log("Response Type: %s", response.type );
		console.log(response.data);

		//example reading json data
		console.log("Employee id= " + response.data.empID);

	});

 	request.on("error", function(response){
		console.log("Request failed...");
		console.log("Error: " + response.lastError);
	});
```

With the HTTP request you register a callback for one of the events published by the request.

The event `done` is triggered from a successful request. The handler receives a `response` object as argument. We use the `response` object to get our data and other useful information.

The event `error` is triggered for errors and also receives a `response` object. To check which error occurred we use the `response.lastError` property.

Remember that `request.on()`returns a [subscription](./fte_ref_trait_publisher.md) and not the request itself.

## Using a callback ## {#usecallback}

When you specify a callback function in your `http.get()` or `http.post()` the callback will be executed when the request is completed. This callback is called regardless if the request was successful or had an error.

```js
	var request = http.post(url, data, function(response){
		if(response.lastError !=0 ){
			console.log("request failed...");
			return;
		}

		console.log("Response Type: %s", response.type );
		console.log(response.data);
		//example reading json data
		console.log("Employee id= " + response.data.empID);
	});
```

Just like events the callback receives a `response` object as argument.

In your callback you can check the property `response.lastError`.

## Chaining with then ## {#usethen}

Since a request implements the [emitter](./fte_ref_trait_publisher.md) we can add the [thenable](./fte_ref_trait_thenable.md) trait to chain our response with a `then()`.

```js
var params = {
	name: "Jose",
	code: 201,
};

var url = "http://localhost/echo.php";
var s = http.get(url, params).on(function(response){
	console.log(response);
	//do some thing with our Response
	var value = 10;
	return value;
});

//make it thenable
exc.traits.thenable(s).then(function(value){
	console.loge("Got value %s", value);
});
```


## Response Object ## {#response}


Properties of a response object:

| Property | Description |
| -- | -- |
| data | The actual payload. It may be `undefined`, an empty `string`, a `string`, an `object`, or an `array`. |
| type | A string indicating the type of payload received. A `"json"` type means that your data is an `object` or `array`. A `"blob"` type means you got text or binary data specific to your request. A type of `"js"` means that the payload is actual javascript code. |
| status | An integer with the HTTP response status. |
| url |  A string of the final URL obtained after any redirects. |
| lastError | An integer with an error code. Its value is zero for no errors. |

## JSON Payloads ## {#jsonpayload}

A request may send JSON encoded data in the body of a response with the `content-type` of `text/json` or `application/json`.

The JSON payload will be converted to a JS object or array.

An example in PHP will be:
```PHP
<?php
$data_out = json_encode($data);
header("Content-Type: application/json");
print $data_out;
exit;
?>
```

## JavaScript Payloads ## {#jspayload}

A response may contain plain javascript code to be executed by sending the `content-type`  `application/javascript` in this case the `response.type` is set to `js`.

The code is executed automatically by default. To disable automatic execution set the option `opRunJS` to false.

The code executed will have access to the plain object `ajaxResponse`. This object can be used to pass data back to the promise in the `response.data` property. For example:
```js
	ajaxResponse.empName = "Jose";
```

If the option `opRunJS` is false, then `response.data` has the actual javascript code.

> ! Javascript syntax errors will cause the request to fail.

## Receiving arbitrary data ## {#arbitrarydata}

Arbitrary data have a `content-type` set to a value other than the ones expected above. The `response.type` is set to `blob` and the actual string data is in `response.data`.


## JSON Stream ## {#jsonstream}

A JSON Steam allows an API to send a large set or continuous JSON data over a single request.

EXC supports **Record separator-delimited JSON** as a JSON Stream format. See [JSON Streaming in Wikipedia](https://en.wikipedia.org/wiki/JSON_streaming).

When a `content-type` header with `application/json-seq` is received EXC will poll the connection to process JSON records.

Each time a JSON record is received a `"value"` event will be triggered with the value as the first argument.

```JS
	request.on("value", function(value, response){
		console.log(response);
		console.log("Response Type: %s", response.type );

		console.log("entry %o", value);
	});
```

**NOTE:** The `"done"` event will be executed when the streaming stops. The `response.data` property will be an empty object for a JSON Stream.

## Canceling a request ## {#cancel}

Use the function `request.abort()` to cancel a request. An "error" event will be triggered with the `response.lastError` set to `503`.

## HTTP Error Codes ##

| Code | Description |
| -- | -- |
| 501 | Request time out. |
| 502 | An unspecified XHR error. |
| 503 | Request aborted or interrupted. |
| 504 | The HTTP status of the response is not a 200. |
| 510 | Access forbidden, HTTP Status 403. |
| 511 | The resource was not found, HTTP Status 404. |
| 520 | Invalid JSON, unable to parse JSON. |
| 521 | Invalid javascript code, syntax error or runtime error. |
