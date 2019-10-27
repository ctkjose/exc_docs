[Frontend](./fte_index.md) {menu}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>


# Backend # {#backend}

This documents describe the functionality available to interact with your backend.

EXC implements a [Backend for Frontend](https://samnewman.io/patterns/architectural/bff/) (BFF) architecture to ease the development of an application. In this pattern instead of having a general-purpose API, your app has a backend component tailored for your app needs. This BFF
makes app design and security easier and we can handle downstream API and other sensitive stuff on the server side.

The built-in functionality described here works with EXC's backend framework and requires no configurations, routes or middleware to get you running. If you are using another backend technology EXC has libraries for requests and models that should get you running with other API based backends.

The object `exc.backend` provides the functionality to interact with your backend.





# Functions #

## backend.action() ## {#backend-fn-action}

Creates an instance of an action implemented by a [backend controller](./bke_ref_controller.md).

```js
var action = exc.backend.action("[app.getrecord]");
```

```js
var action = exc.backend.action("[app.getrecord]", data);
```

### Parameters ###

**action** (string)(required) A controller action string in the format `[aControllerName.aMessageName]`.

> ? If you need to invoke an action from a backend other than your current app (eg: a shared backend or web service) you can specify the url of the backend with the following syntax: `@(https://example.com/mybackend/index.php)[aControllerName.aMessageName]`.

**data** (object)(optional) A plain object with key-value pairs that will be send to the backend action.

# Action # {#backend-action}

The object `exc.backend` provides the functionality to interact with your backend.

# Functions #


## action.exec() ## {#action-fn-exec}

Executes the backend action.

```js
var action = exc.backend.action("[app.getrecord]");
var promise = action.exec(data);
```
### Parameters ###

**data** (object)(optional) A plain object with key-value pairs that will be send to the backend action.

### Return value ###

An instance of a `core.promise`.
