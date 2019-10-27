**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Models #

A model is a set of data to be manipulated by our controllers.




## Models from Views ## {#mcreatev}

In our HTML we can explicitly declare a model and bind components or other html elements to a model's field.

A model for a view is created with the attribute `m-model`.

```HTML
<div class="view" name="vFindUser" data-controller="ctlUsers" m-model="findFields">
```

Here we defined or associated the model `findFields` with our view.

By default our model is available using the namespace `exc.models` for example:

```js
	exc.models.findfields.username = "ctk";
```

### Bind a component ### {#mbindcmp}

Inside our view we can bind components to a field in a model using the `m-field` attribute. For example:

```HTML
<w type="textbox"  name="fldUsername" placeholder="Username" m-field="username"></w>
```
In this example the value of our component is associated to the key `username` of the model `findFields`.

If the value of the component changes the value of the key in our model is also updated, and if we change the value of our key the component is updated.

Another option is to add the attribute `m-field` without a field name, in which case the component is associated with a key that has the same name as the component. For example:

```HTML
<w type="textbox"  name="fldUsername" placeholder="Username" m-field></w>
```

This component is associated with a key named `fldUsername` in our model.

## Bind an element ## {#mbindnode}

The attribute `m-field` can also be used to bind an element to a key value (one-way binding).

```HTML
<span m-field="$findFields.username"></span>
```
Using backend [templates](doc_server_view.md) you can use the `model` directive:

```HTML
{{#model::findFields.username}}
```

## Observing a key ## {#observing}

You can register an observer to be notified when a key in the model is modified.

Use `myModel.addObserverForKey(keyName, fnCallback, [cookie])` to register an observer and `myModel.removeObserverForKey(keyName, fnCallback)` to remove the observer.


```js
var myObserver = function(aModel, keyName){
	console.log("The Key [%s] changed to ", keyName, aModel[keyName]);
};

exc.models.findFields.addObserverForKey("username", myObserver);
```

## Observing the model ## {#observingm}

We can also add an observer to be notified of any changes in the model with `myModel.addObserver(fnCallback, [cookie])`, for example:

```js
var myObserver = function(aModel, keyName){
	console.log("The Key [%s] changed to ", keyName, aModel[keyName]);
};

exc.models.findFields.addObserver(myObserver);
```


# Model Object # {#modelobj}

## Constructor ## {#modelconst}

```js
var model = new exc.model();
```

```js
var model = new exc.model(items);
```

## model.keys ## {#modelkeys}

```js
aModel.keys.forEach(function(key){
	console.log("Key=%s", key);
});
```
## model.haskey ## {#modelhkeys}
```js
if( aModel.hasKey(keyName) ){
	console.log("has key %s", keyName);
}
```

## model.serialize ## {#modelserialize}

```js
var json = aModel.serialize();
```

## model.asPrimitive ## {#modelprim}

```js
var plainObject = aModel.asPrimitive();
```
