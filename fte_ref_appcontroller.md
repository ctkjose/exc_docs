[Frontend](./fte_index.md) {menu:}
[Related]() {menu:}
[Controller's Manifest](./doc_client_controller_manifest.md){menu:}
[Interacting with the front-end](./doc_server_client.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>


# App Controller #

The **app controller** is responsible for the orchestration of your app's entire life-cycle and every application must have an app [controller](./fte_ref_controllers.md).

The app controller is actually the only controller required in an EXC app, yet as your app grows in complexity we can add other [controllers](./fte_ref_controllers.md) to better structure our app logic, made the code more manageable and add reusability.

EXC uses a global object named `app` as your app controller. When EXC is loading it will wait until a variable named `app` appears on the [global](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Global_variables) scope (In a browser thats the [window](https://developer.mozilla.org/en-US/docs/Web/API/Window) or [self](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Global_variables)).

The `app` object can be inline inside your HTML in a `<script>` tag or loaded dynamically.

```html
<html>
<head>...</head>
<body>
...
<script type='text/javascript'>
app = {
	initialize: function(){
		//app object was loaded as the app controller
	}
};
</script>
</body>
</html>
```
