# Value Provider  Interface #

## ValueProvider interface in components. ##

A component will have a property named `vs` of type `valueStore` to hold its native value.

In `exc.components.installValueInterface(mainNode)` we setup a component to implement a Value Provider interface.

A `component` implements a `setter` function used to update its UI.

When a `component` changes a value it informs the `VS` by using its `mainNode.assignValue(value)` function.

The `component` is observing the `VS` whenever its value is changed the `callback` is executed, the callback then calls the `setter`.  The `callback` will exit if the change was originated by the component itself, it does this by checking the flag `mainNode._vsCBO`.

**Note:** The function `mainNode.assignValue(v)` sets the flag `mainNode._vsCBO` (*"Change by Owner"*) to true. When set to true the component ignores the notification received form its `vs.setValue(v)`.

The `component` implements the functions `mainNode.setValue(v)` and `mainNode.getValue()`. Both of these functions are just wrappers to `mainNode.vs.setValue(v)` and `mainNode.vs.getValue()`.

### Notes when building Components. ##

A `build()` function of a component must call the `exc.components.installValueInterface(mainNode)` function passing the DOM Element (or node) that represents your components UI.

This element must have the valid `[data-uiw]` attribute of your component.
