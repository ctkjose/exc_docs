[Related](){menu:}
[Back-end Framework](./bke_index.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Routes # {#routes}

This sections explains the routing conventions used by EXC when handling requests and URLs.

To better understand the internals of how routing works on EXC is important to understand that all requests to your app are handled by your `index.php` file. Your `index.php` file is a generic file that all it does is call the exc's bootloader. The bootloader takes care of figuring out how to handle a request.

There are two basic requests supported by EXC.

### Traditional Requests ### {#requesttra}

A traditional request is one where the actual [URL](https://en.wikipedia.org/wiki/URL) components points to a physical php file or resource (css, js, etc) on the filesystem. For example: `https://example.com/myapp/index.php`.

### Controller Actions ### {#requestctlact}

Since everything in EXC is handled by a controller the URL in a traditional request must specify an **action** using the [query parameter](https://en.wikipedia.org/wiki/URL) `a`.

| URL | Controller | Message |
| -- | -- | -- |
| `https://example.com/myapp/index.php?a=getRecord` | `appController` | `action_getRecord` |
| `https://example.com/myapp/index.php?a=employee.getRecord` | `employeeController` | `action_getRecord` |

The query parameter `a` indicates the name of the action to be executed by your controller. By default this action is [mapped](./bke_ref_controller.md) to a message or function in your `appController` instance.

> NOTE: Your `appController` is the default handler of all messages unless another controller is specified.

When the action is directed to a controller other than `appController` then the query parameter `a` includes the controller name and the message, like in the example `a=employee.getRecord`.

For security reasons the actual function that gets executed in your controller is not exactly the name provided in the URL. A controller action that can be executed from an URL request must have a name starting with `action_`. In our previous examples `getRecord` is mapped to a function named `action_getRecord`.

### API Requests ### {#requestapi}

EXC routes API requests based on the URL components and the [HTTP methods](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods).

The default logic for API requests is explained in this table:

| HTTP METHOD | URL | Controller | Message |
| -- | -- | -- | -- |
| GET | `https://example.com/myapp/records` | `recordsController` | `action_get` |
| POST | `https://example.com/myapp/records` | `recordsController` | `action_add` |
| PUT | `https://example.com/myapp/records` | `recordsController` | `action_update` |
| DELETE | `https://example.com/myapp/records` | `recordsController` | `action_delete` |

The default handling of API requests only supports parameters using the query string for example:

`https://example.com/myapp/records?id=125`

In the case of a URL with PSR-7 styles parameters you must use the [$request](./bke_ref_request.md) object to access the URL components. For example a GET request with the URL:

`https://example.com/myapp/records/125/edit`

EXC will invoke the function `recordsController.action_get()`. In this function we can access the additional elements using the [$request](./bke_ref_request.md) object.

```PHP
class recordsController extends \exc\controller\apiController {

	public function action_get($params){

		$request = \exc\app::request();
		$id = $request->elements[0]; //returns 125
		$operation = $request->elements[1]; //returns "edit"

	}

}
```

### Routing API requests ###

To handle API request with PSR-7 like urls we use API routes. API routes are defined under an `api` key in your [appController](./bke_ref_appcontroller.md) `config()` function. Lets see an example of a route:

```PHP
$options['api'] = [
	'/records/{$id}' => [
		'accepts_methods' => ['get'],
		'action'=> 'aController.getRecord',
	]
];
```

The key of a route entry is a URL pattern, in our example a request must match the pattern `/records/{$id}` as in the following url:

`https://example.com/myapp/records/125`

In a pattern we specify a parameter using a `{}`, inside the braces we specify the parameter name for example `{$id}` defines the parameter `id`. The following table demonstrate some additional examples of route patterns:

| Pattern | URL |
| -- | -- |
| `/records` | `https://example.com/myapp/records` |
| `/records/` | `https://example.com/myapp/records/` |
| `/records/{$id}` | `https://example.com/myapp/records/125` |
| `/records/{$id}/edit` | `https://example.com/myapp/records/125/edit` |
| `/records/{$id}/{$action}` | `https://example.com/myapp/records/125/remove` |
| `/agency/{$orgid}/accounts/{$id}` | `https://example.com/myapp/agency/001/accounts/125` |

The `accepts_methods` key is an array of HTTP methods that are valid for this route. Possible values are "get", "post", "put", "delete".

The `action` key is the actual controller action to be executed. It's given in the format `[controllerName.message]`.
