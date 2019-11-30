[Related](){menu:}
[Back-end Framework](./bke_index.md){menu:}
[Session and data persistence](./doc_session.md){menu:}
[Request](./bke_ref_request.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>


# Interacting with the front-end #
*This topic applies to the BackEnd framework.*


## Getting data ## {#gettingdata}

Data and parameters received on a request are available using the [request](./bke_ref_request.md) object.


## Client ## {#client}

The client object allows your backend to interact with your application's frond-end running in the users browser.

```php
<?php
	$client = \exc\client::instance();
```

# Interactions with your front-end #

You can use the `$client` to make common interactions with your client easily.

> Note: Most methods of `$client` are chainable. Examples here do not use chaining for sake of simplicity.

## The shared session ## {#shrdsess}

A [shared session](./doc_session.md) is a set of key-value pairs that persists between request on the server side and are shared automatically with your front-end so you can use them in javascript.

> ! For security reasons the server always overrides the session values on the client.

## Setting a key ## {#shrdkeyset}

```php
<?php
$client->session($key, $value);
```

## Getting the value of a key ##  {#shrdkeyget}

```php
<?php
$value = $client->session($key);
```

## Testing if a key exists ##  {#shrdkeytest}

```php
<?php
$ok = $client->sessionHasKey($key);
```

## Removing a key ## {#shrdkeyrm}

```php
<?php
$client->sessionRemove($key);
```

## Publish a message or event ## {#pubmsg}

Similar to controllers on the backend, you can publish a message to your javascript controllers.

```php
<?php
$client->publish("displayRecord", ["id"=>20]);
```

## Adding a javascript file ## {#addjsfile}

```php
<?php
$client->addJSFile($aPath);
$client->addJSFile('asset://js/myfile.js'); //may use exc's special url protocols
```

## Adding a css file ## {#addcssfile}

```php
<?php
$client->addCSSFile($aPath);
$client->addCSSFile('asset://css/mystyle.css'); //may use exc's special url protocols
```

## Adding a javascript controller file ## {#addjscontroller}

```php
<?php
$client->addController($objectName, $aPath);
$client->addController('recordController', 'asset://js/controller.record.js'); //may use exc's special url protocols
```

## Set application data ## {#appdataset}

```php
<?php
$aValue = ["id"=>25, "name"=>"Jose", "lname"=>"Cuevas"];
$client->setData("record", $aValue);
```

> Data is available in your front-end using the property `app.data`, for example `app.data.record.name`.

## Respond to a request ## {#apprespset}

When the front-end sends a request that expects data back we use `$client->sendResponseData($data)` to send the data. Lets see a very simple example:

```php
<?php
class appController extends \exc\controller\viewController {
	public function onAction_ComputeRate(){
		$client = \exc\client::instance();
		$qty = $client->values['qty'];

		$results = ["status"=>200, "cost"=>25 * $qty, "qty"=>$qty];
		$client->sendResponseData($results);

		$client->done(); //finish the interaction
	}
```
In our front-end we invoke our service using a backend action.
```js

	var action = exc.backend.action("@(app.computeRate)"); //create a backend action
	action.params.qty = 2; //set a parameter

	action.exec().then(function(data){
		alert("The cost is:" + data.cost);
	});
```

## Run javascript code ## {#runjs}

```php
<?php
$jsCode = 'alert("Hello Jose");';
$client->runCode($jsCode);
```

## Run javascript when the application is ready ## {#runwhenready}

```php
<?php
$jsCode = 'alert("Hello Jose");';
$client->ready($jsCode);
```

# Related Topics #

[Session and data persistence](./doc_session.md)<BR>
