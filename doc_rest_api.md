[Related](){menu:}
[Interacting with the backend](./fte_ref_backend.md){menu:}
[Interacting with the frontend](./doc_server_client.md){menu:}
[HTTP Requests](./doc_score_ajax.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>


# Restful API in EXC #

We understand that there are many emerging standards for JSON based APIs like [JSON-API](https://jsonapi.org/format/), and things like GraphQL, OData/Swagger, and a million of RESTful guides on best practices.

JSON-API and OData both try to introduce a **data type** or schema which is useful in some scenarios where composition and exchange of data from multiple sources is a big thing, sort of remind me of big XML workflows and orchestrations, but for most cases it adds a significant overhead when operating with data. I personally like many of the concepts in JSON-API, yet it does adds a bit of complexity and overhead for most use cases, in particular is hard to avoid having to inject some type of middleware in-between the JSON-API and your app.

The choice for implementing a RESTful API in EXC came down to simple-is-better; so EXC adopts some conventions from JSON-API but for the most parts its pretty straight forward to implement a RESTful api using EXC.

This document is sort of humble little reference guide to EXC's standard for RESTful api.

# JSON Document # {#jsondoc}

APIs built with EXC use the concept of a JSON Document. A Document is a basic object defined in JavaScript Object Notation or **JSON** [RFC7159](http://tools.ietf.org/html/rfc7159).

Every document object **MUST** contain a `type` key. A `type` key is a string that describes the type of object.

A Document **MAY** include an `id` key. This key is a user specific identifier in the context of the API and document `type`.

A document **MUST** include either a `data` key or an `error` key.

A `data` key is used to represent the object's primary data. In essence we used this key to return values and data. When a document represents a failure state the `data` key must not be present and an `error` object should be used.

```js
{
    "type": "employee",
    "id": "92534",
    "data": {
        "name_first": "Jose",
        "name_last": "Cuevas",
		"job_title": "Software Developer",
		"job_code": 24600,
    }
}
```
When a document has a collection of records or data the `data` is an array of objects. By convention if a document has no data (*of a valid and and successful request*), the document **MUST** include a `data` key with an empty array (`[]`).

An `error` key is an object containing additional info regarding an error. The `error` object may include a `message` key with a human readable error message. A `code` key may be included with a API specific error code.

It may also include a `detail` key with an array of objects. Each object in the array includes information regarding the error. The format of an entry in the `detail` array is specific to the API.

```js
{
    "type": "response",
    "status": "ERROR",
    "error": {
        "code": 501,
        "message": "invalid field"
    }
}
```


# API Response # {#api-resp}

A **response** to an API request is always a JSON Document. The document has the following keys:

| Key | Description |
| -- | -- |
| type | Value is always `response`. |
| status | Is defined by the API. For EXC internal API the value is either "SUCCESS", "ERROR" or an error code. |
| data | An optional object or an array with data. |
| error | An optional object with details related to an ERROR. If `error` is present then  `data` will not be present and vice versa.  |

An error will cause the response to have an `error` object.

```js
{
    "type": "response",
    "status": "ERROR",
    "error": {
        "code": 501,
        "message": "invalid field"
    }
}
```


```js
{
	"type": "response",
	"status":"SUCCESS",
	"id": id, //id of this request
	"data": [
		... //additional keys for params or data
	]
}
```

```js
{
	"status":"SUCCESS",
	"data": {


	}
}
```

```js
{
	"status":"FAILED",
	"error":{
    	"code": "FIELDS_VALIDATION_ERROR",
    	"message": "One or more fields raised validation errors.",
		"debug_id": "A1233AEFH0001", //unique REF num to debug
    	"details": [
      		{ "field": "email", "issue": "Invalid email address." },
			{ "field": "password", "issue": "Password too short." },
    	]
  	}
}
```



## Top-Secret? ##

Backend and Client interactions use standard API channels as provided by EXC.

A client action is a JSON document with a type of `exc_req`. A request document must include:

| Key | Description |
| -- | -- |
| type | A JSON document `type` attribute. Value is always `exc_req`. |
| id | A JSON document meta field. A reference assigned to a backend request. Used to manage a response que. The backend will echo this `id` in a field with the key `exc_req_id`. |
| sa | A JSON document meta field. A string in the standard format `controller.action` that specify a particular handler for the action. |
| data | An optional plain object with data and parameters. |



A backend response is a JSON Document with a type of `ebrsp`.
