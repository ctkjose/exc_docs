**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Interacting with the front-end #
*This topic applies to the BackEnd framework.*

## Client ##

The client object allows your backend to interact with your application's frond-end running in the users browser.

```php
<?php
	$client = \exc\client::instance();
```

Data received from a client is available using the `$client->values` property. The `values` property is an array of key value pairs.
```PHP
<?php
$zipcode = $client->values['zipcode'];
```


# Interactions with your front-end #

## Setting a shared session key ##

```php
<?php
$client->session($key, $value);
```

## Getting the value of a shared session key ##

```php
<?php
$value = $client->session($key);
```

## Testing if a shared session key exists ##

```php
<?php
$ok = $client->sessionHasKey($key);
```

## Removing a shared session key ##

```php
<?php
$client->sessionRemove($key);
```

## Publish a message or event ##

```php
<?php
$client->publish("displayRecord", ["id"=>20]);
```

## Adding a javascript file ##

```php
<?php
$client->addJSFile($aPath);
$client->addJSFile('asset://js/myfile.js'); //may use exc's special url protocols
```

## Adding a css file ##

```php
<?php
$client->addCSSFile($aPath);
$client->addCSSFile('asset://css/mystyle.css'); //may use exc's special url protocols
```

## Adding a javascript controller file ##

```php
<?php
$client->addController($objectName, $aPath);
$client->addController('recordController', 'asset://js/controller.record.js'); //may use exc's special url protocols
```

## Set application data ##

```php
<?php
$aValue = ["id"=>25, "name"=>"Jose", "lname"=>"Cuevas"];
$client->setData("record", $aValue);
```

> Data is available in your front-end using the property `app.data`, for example `app.data.record.name`.

## Set the response for a request ##

When the front-end sends a request that expects data back we use `$client->sendResponseData($data)` to send the data. Lets see a very simple example:

```php
<?php
class mainController extends \exc\controller\viewController {
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

	var action = exc.backend.action("@(main.computeRate)"); //create a backend action
	action.params.qty = 2; //set a parameter

	action.exec().then(function(data){
		alert("The cost is:" + data.cost);
	});
```

## Run javascript code ##

```php
<?php
$jsCode = 'alert("Hello Jose");';
$client->runCode($jsCode);
```

## Run javascript when the application is ready ##

```php
<?php
$jsCode = 'alert("Hello Jose");';
$client->ready($jsCode);
```

# Related Topics #

[Session and data persistence](./doc_session.md)<BR>
