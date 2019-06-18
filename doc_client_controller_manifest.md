**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# The Controller's Manifest #
*This topic applies to the Front-End framework.*

Any controller may include a `manifest` property. A `manifest` is used to specify dependencies or additional assets used by a controller.

There are two categories of items in a manifest the `require` and the `include` items. A `require` item must be loaded for a controller to be available and `include` items are loaded asynchronously without affecting the controller.

The `manifest` is a plain object property that you add to your controller with items in either a `require` or `include` property.

> Items in a manifest are fetched only once, regardless if other controllers request the same item.

Example of a `manifest` property in the `app` controller:

```js
self.app = {
	manifest: {
		require: [
			{controller: "loginController", url: "../a/js/controller.login.js", wait: true},
		]
	},
	initialize: function(){
		//app object was loaded as the app controller
	},
	onAppStart: function(){
		console.log("App is running...");
	}
};
```
# Items in your manifest #

An item in the `manifest` may be a:

### Controller Item ###

This item fetches and installs a controller.

| Key | Description |
| -- | -- |
| controller | a string with the name of the global object that holds the controller |
| url | a string with the url of the file with the controller js code. |
| wait | a boolean that indicates if this item is loaded synchronously. |

### Asset Item ###

This item fetches and makes available a css or js file.

| Key | Description |
| -- | -- |
| url | a string with the url of the file. |
| wait | a boolean that indicates if this item is loaded synchronously. |



# Related Topics #

[Controllers](./doc_client_controllers.md)<BR>
