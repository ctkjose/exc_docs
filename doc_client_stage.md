**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>


# Working with views and the stage #
*This topic applies to the Front-End framework.*

# Stage #

The stage is what we call the layout where we display your UI. A stage is made of multiple views through which the user navigates.

The object `exc.stage` provides the functionality to manage the views and handle the basic navigation between your views. 

## Showing a View ##

```js
	exc.stage.show(aView, options);
```

### Parameters ###

`aView` is an instance of a `view` object or a string with the name of an existing view.

`options` is a plain object with options. This parameter is optional.

| Option | Description |
| -- | -- |
| 'modal' | boolean, indicates if view is displayed as a modal panel. default value is false. |
| 'push' | boolean, indicates if view is added to the navigation stack. default value is true. |

The function `exc.stage.show()` displays a view on the stage and adds it to the navigation stack.

## Closing the current View ##

```js
	exc.stage.closeCurrent();
```

Closes the current view on the stage and pops the previous one into view.

## Events & Messages ##

| Event | Parameters | Description |
| -- | -- | -- |
| `viewShouldClose` | stage message | Tells the controller that a request to remove a view form the stage was received. |
| `viewWillEnterStage` | stage message | Tells the controller that a request to add the view to the stage was received. |
| `viewDidEnterStage` | stage message | Tells the controller that a view is on the stage. |
| `viewWillClose` | `view` | Tells the controller that view is about to close. |
| `viewShow` | `view` | Tells the controller that view displayed. |
| `viewChanged` | `view` | Tells the controller that a view's contents changed. |

### Stage Message ###

The parameter of message handler for a **stage message** is just a plain object with the members `stage` and an optional `view`.
```js
	viewShouldClose: function(msg){
		console.log("Stage: %o", msg.stage);
		console.log("View: %o", msg.view);
	}
```

# Related Topics #

[Views](./doc_client_view.md)<BR>
