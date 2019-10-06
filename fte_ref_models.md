**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Models #



## Binding to a component ## {#mbindw}

A component can be bind to a field in a model using the `m-model` attribute. For example:

```HTML
<w type="textbox"  name="fldUsername" placeholder="Username" m-model="myRecord.username"></w>
```
In this example the model is `myRecord` and the value of our component is associated to the key `username` of the model.

If the value of the component changes the value of the key in our model is also updated, and if we change the value of our key the component is updated.

Another option is to set the attribute `m-model` to just the name of the model in which case the component is associated with a key that has the same name as the component. For example:

```HTML
<w type="textbox"  name="fldUsername" placeholder="Username" m-model="myRecord"></w>
```

This component is associated with a key named `fldUsername` in our model.

## Create a model from a View ## {#mcreatefromw}

If you have components bounded to a model with a `m-model` attribute in a view you can create a model  
