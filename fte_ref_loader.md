[Frontend](./fte_index.md) {menu:}
[Related]() {menu:}
[Controller's Manifest](./doc_client_controller_manifest.md){menu:}
[Interacting with the front-end](./doc_server_client.md){menu:}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Loader # {#loader}

Loader is a promise based resource loader.


## Basic use ##

```js
var manifest = loader.create();

manifest.addScript("js/include1.js");

manifest.addScript({url: "js/include2.js", done:function(data){
	console.log("got data...");
	console.log("data = %o", data);
}});

manifest.addModule("js/exc/module1.js");
manifest.addCSS("css/test.css");

manifest.process().then(function(){
	console.log("manifest is done");
});
```
## Paths ## {#paths}

URLs are relative to the current path. If your web app is available at "https://example.com/myapp" then a resource like "js/mylib.js" will be loaded from: "https://example.com/js/mylib.js".

When you specify a resources's url you can always use absolute paths starting with http/https.

You can also configure `paths` to simplify from where your resource is loaded.

```js
loader.config.paths = {
	'modules': 'js/mylib'
	'alib': 'js/vendor/alib.js'
}

//now we can use "modules" as a shorthand
manifest.addModule("modules/module1.js");
manifest.addModule("modules/module2.js");

//we can use a path as an alias too!
manifest.addScript("alib");
```

## Require ## {#requires}

The loader provides basic support for CommonJS style of modules. This allows you to use CommonJS and NodeJS modules.

Which modules you can use depends on how the module was written. For example a module that uses NodeJS functionality can not be used on the browser. Modules that have dependencies on other modules are also not supported since a CommonJS's `require()` function is not available.

> * For modules that depend on `require()` we suggest you check [browserify](http://browserify.org).

To add a CommonJS style of module use the `addModule()` function or set the [entry](#fnaddEntry) type to `"module"`.

> ! Notice that CommonJS's modules should not be confused with EXC modules, which also may be loaded using `addModule()`.

The loader offers its own stand-in `require()` function to access these modules.

```js
loader.create('js/aModule.js').process().then(function(){
	console.log("everything is loaded...");
	var aLib = loader.require('js/aModule.js');
});
```

At this time [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD) style of modules are not supported.

## Data ## {#jsondata}

You may use the loader to get json data, this is useful for configuration, etc.

To add a json resource use the `addJSON()` function or set the [entry](#fnaddEntry) type to `"json"`.

You can use the `require()` function to access the data.

```js
loader.create({'url':'js/myconfig.js', 'type':'json'}).process().then(function(){
	console.log("everything is loaded...");
	var data = loader.require('js/myconfig.js');
});
```

When using the functions `addJSON()` or `addEntry()` you can specify a callback with the `done` key. This callback will also receive the json data on its first argument.

A javascript resource created with `addScript()` or using the type `js` may also return data. For example:

```js
//file1.js

return {name:"Joe", lname:"Cuevas"};
```

When this file is loaded the data on its `return` statement will also be available using `loader.require()`.

```js
loader.process().then(function(){
	var data = loader.require('js/file1.js');
	console.log("name=" + data.name);
});
```

# Loader Reference # {#objref}


## loader.addEntry(entry) ## {#fnaddEntry}

Use addEntry() to add a resource to be fetched by loader.


### Parameter entry ###

The parameter entry is a plain object defining the resources.

| Key | Description |
| -- | -- |
| url | *Required* The url of the resource.<br> |
| type | *Required* A string indicating the resource type. Valid values are "js", "json", "css", "module". |
| done | An optional callback function to be executed when the file is available and loaded. |
| is_required | Boolean loader will fail if unable to load this resource. Default is true. |
| is_forced | Boolean telling the loader that this resource must be loaded everytime regardless of its cache status. |


The functions `addScript()`, `addCSS()` and `addModule()` are shorthands to `addEntry()` that set the corresponding `type`.

## loader.process() ## {#fnprocess}

Call `process()` to start loading the resources.

This function returns a promise that is resolved if all of the required resources were loaded.

Prior to resolving the promise any callbacks registered for a file using the `done` key will be executed.

## loader.create() ## {#fnacreate}

Use `create()` to get an instance of a loader.

This factory function accepts an array of entries as used by the function `addEntry()`.

```js

var items = [
	{'url': 'js/mylib.js'},
	{'url': 'css/mystyle.css'}
];

loader.create(items).process().then(function(){
	console.log("everything is loaded...");
});
```

You may also specify each entry as an argument to this function.

```js
loader.create('js/mylib.js', {'url': 'js/myconfig.js', 'type': 'json'}).process().then(function(){
	console.log("everything is loaded...");
});
```
