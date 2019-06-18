**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Session and persistent data #
*This topic applies to both the Backend and Front-End frameworks.*

Your backend application is ran on a request basis without any persistence of data or variables. EXC uses a **session** to keep a tab on the state of your application and maintain persistence.

The **session** allows to store values that are available from one request to another. We use the **session** for things like: what user is using our app, which record are editing, etc.

### Saving a value in the Session. ###

In the backend we use the `$client` object to store values in the session that are automatically available to our front-end. For example:

```php
<?php
	$client = \exc\client::instance();

	//save our recordID in the session
	$client->session("recordID", $recordID);
```

In our front-end the `app` controller has similar functions to access the session. For example to read the session value `"recordID"` we use `app.session()`:
```js
	var recordID = app.session("recordID");
```

We can test if a value is already stored in the session using `$client->sessionHasKey()`:

```php
<?php
	$client = \exc\client::instance();

	if($client->sessionHasKey("recordID")){
		$recordID = $client->session("recordID"); //get its value
	}
```

In javascript we check if a session value is set in a similar fashion using the `app` controller.
```js
	if(app.sessionHasKey("recordID")){
		console.log("We have a record");
		var id = app.session("recordID");
	}
```

## Session Functions ##

| Use | PHP Backend | JS Front-end |
| -- | -- | -- |
| Test if key exists in session. | `$client->sessionHasKey($keyName)` | `app.sessionHasKey(keyName)` |
| Removes a key from session. | `$client->sessionRemove($keyName)` | `app.sessionRemove(keyName)` |
| Get value of a session key. | `$value = $client->session($keyName)` | `value = app.session(keyName)` |
| Sets the value of a session key. | `$client->session($keyName, $value)` | `app.session(keyName, value)` |

> Note: On the front-end none of the session functions (`app.session()` or `app.sessionRemove()`) will modify the session data on the backend.

## Considerations using session ##

Given the nature of web apps, EXC is designed to reduce the ability of malicious individuals to tamper with your session data.

Like with any web application is a good design principle to consider all of your front-end data **untrusted**.

The session data that we manage with `$client` in your **backend** is pushed to your *front-end* on each interaction, like a one-way synchronization.

By design setting the value of a session key in your front-end using `app.session(key,value)` will NOT update the value on the session managed by the backend. Values stored in front-end are saved locally in the browser using the [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) and are available as long as the browser remains open.

### Backend Session ###

In many instances it is important to keep session data privately in the backend, specially for security reasons, the less data we expose in the front-end the best.

The `\exc\session` class allows you to store and manage session data.

```PHP
<?php
	//save our recordID in the session
	\exc\session::key("recordID", $recordID);

	//check if key is in the session
	if(\exc\session::hasKey("recordID")){
		//read our key
		$recordID = \exc\session::key("recordID");
	}
```

While values managed using the session functions found in `$client` are synchronized with the front-end, `\exc\session` is strictly for storing values on the backend.

Functions of `\exc\session`:

| Function | Description1 |
| -- | -- |
| key($keyName) | Returns the value of a key if exists, else returns `null`. |
| key($keyName, $value) | Store a value in the session. |
| hasKey($keyName) | Returns true is a value is stored in the session. |
| removeKey($keyName) | Removes a value from the session. |
| destroy() | Removes all values and resets the session. |

> **NOTE** Internally the `$client` session uses `\exc\session` to store its values under the key `"ms"`, which is itself an array of key-value pairs.
