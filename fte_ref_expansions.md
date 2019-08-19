**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Expansions #

Use expansions to extend your HTML. An expansion adds a special behavior to html elements or components.

The first type of expansions are "magic" attributes. Magic attributes start with `"m-"`.


## Basic magic attributes ##

You can add basic action handlers to many of the common events. EXC provides magic handler for the following events: `click`, `change`, `blur`, `focus`, `dblclick`, `keydown`, `keyup`, `keypress`, `mouseenter`, `mousemove`, `mouseover`, `mouseup`, `copy`, `cut`, `paste`.

You add an event action handler with an attribute that starts with `m-` followed by the event name. For example `m-click`.

```HTML
<w name='myButton' type='btn'  m-click='[doClick]'>Click Me</w>
```

The value of this attribute could be on of the following actions:

| Type | Syntax | Description |
| -- | -- | -- |
| Message | `[message_name]` | Publishes a message.<br><br>For Example:<br><br> `m-click="[check_record_status]"` |
| Controller Function | `[controllerName.fnName]` | Executes the function `fnName` of the controller specified by `controllerName`.<br><br>For Example:<br><br> `m-click="[myEmpRecordController.checkRecord]"` |
| Enable | `enable($componentName)` | Enables the component specified.<br><br>For Example:<br><br> `m-click="enable($fld_emp_salary)"` |
| Disable | `disable($componentName)` | Enables the component specified. |

## Confirm ##

The attribute `m-confirm` adds a confirmation dialog. The value of the attribute `m-confirm` is the prompt.  

This option will call `event.stopImmediatePropagation()` and `event.preventDefault()` when the user cancels or rejects the confirmation.

The confirmation is executed on the `click` event.

This expansions uses EXC's flavor of `action events`, in particular it will be triggered on the `click.action.validation()` stage to allow to cancel further actions without relaying on `stopImmediatePropagation()` and `preventDefault()`.

We suggest that when using this expansion that you add your own event handlers using `click.action.handler()` or `click.action.done()`, for example:
```js
	var myBtn = exc.component.get('btnDeleteRecord');
	myBtn.onAction("click.action.handler()", function(event){
		console.log("deleting the record...");
		//my delete code here..
	});

```

The confirmation will not be triggered if the component has the `is-disabled` class.
