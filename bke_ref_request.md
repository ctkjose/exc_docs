[Related](){menu:}
[Back-end Framework](./bke_index.md){menu:}
[Client Interactions](./doc_server_client.md){menu:}
[Session and data persistence](./doc_session.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Request # {#request}


Use the `request` object to get information regarding the HTTP request that is currently being handle by your back-end app.

## Using the request ## {#gettingreq}

Obtain an instance of the request object with `\exc\app::request()`.
```PHP
$request = \exc\app::request();
```

## Getting parameters ## {#getparams}

Traditional data send using **query params** (the parameters after a ?) or form data send over an **HTTP POST** is available using the `params` array:

```PHP
//for a url like http://example.com/myapp.php?id=125&type=emp

$request = \exc\app::request();

$id = $request->params['id'];

$type = 'visitor';
if( $request->has('type') ){
	$type = $request->params['type'];
}

```

## Getting a JSON document ## {#getjson}

JSON data send with a `content-type` of `application/json` or `application/vnd.api+json` is available in the `document` array. JSON data is the de-facto payload that your back-end will receive from API requests and most interactions with an EXC front-end.

```PHP
$request = \exc\app::request();

$id = $request->document['id'];
```



# REQUEST Object # {#reqobj}

## Property $method ## {#propmethod}

A string with the corresponding HTTP method used, for example "get", "post", "delete", "put".

`$method = $request->method`

## Property $params ## {#propparams}

Array of key-value pairs representing HTTP query-params or form data.

`$value = $request->params['name']`

## Property $document ## {#propdoc}

Array of key-value pairs representing a JSON payload.

`$value = $request->document['name']`

## Property $elements ## {#propelem}

A zero based array with the URL elements of an API request.

`$value = $request->elements[0]`

## Function has() ## {#fnhas}

Returns true if a key exists in the request parameters, else it returns false.

`$request->has($keyName)`

### Parameters ###

**$keyName** (string)(required) The name of the key to test.

## Function param() ## {#fnparam}

Returns the value of a request parameter if exists.

`$value = $request->param($keyName, $defaultValue)`

Returns the value of the parameter if present, else it returns the value passed in `$defaultValue` or else it returns NULL.

### Parameters ###

**$keyName** (string)(required) The name of the key to get its value.<br>
**$defaultValue** (any)(optional) The value to return if the key is not present.<br>
