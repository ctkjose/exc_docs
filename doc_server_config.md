**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Backend Application Configuration #
*This topic applies to the BackEnd framework.*

The file `app.php` is where we tell EXC about our application and configure it.

Place this file in the root folder of your application, for example:
```
  + /MyApp
    -  app.php
```

Inside your `app.php` we use the global variable `$options` to configure an application.


## CONTROLLERS Option ##

The option key `controllers` is where we list any controller that you may need to be loaded on every interaction with the backend.

```PHP
<?php
//controllers that are installed
$options['controllers']=[
	"recordController" => "app://controller.record.php",
];
```


## MANIFEST Option ##

The `manifest` is EXC's jargon for the list of items like javascript files, css and others that is included in your frontend.

The option key `manifest` is were you specify these resources. This key is an `array` with one entry for each resource.

```js
//files to include in the application manifest
$options['manifest'] = [
	["url"=>'app://assets/js/tests.js'], //global code
	["url"=>'app://assets/js/lib1.js', 'type'=>'export', 'name'=>'mylib'], //an export of a CommonJS Module
];
```

Each entry is a `hash` with the following keys.

| Key | Description |
| -- | -- |
| url | **Required**: A string with the url of the resource.You may use EXC special URL protocols. |
| type | Optional: A string that specify a special handling for the resource. A `type` of `"export"` indicates that this is a javascript file to be handled as a CommonJS Module. A `type` of `"script"` indicates that this is a javascript file that must be executed on the global scope. |
| name | Optional: when `type` is `"export"`, this field has the name of the global variable where a CommonJS module will be available. |
| wait | Optional: A boolean that indicates if a resource is loaded synchronously. This is used to ensure scripts are loaded in a given order. Default value is "true". |


## USING Option ##

The option key `using` is where we add libraries and modules for your backend.

Each entry in `$options['using']` points to a resource that you want to load. For example lets load the library `exc.storage.db`:

```js
$options['using'] = [   //which modules to load
	"exc://exc.storage.db"=>[
		"connections"=>[
			"test1" => ["driver"=>"mysql", "host"=>"192.168.100.175", "port"=>3306, "dbname"=>"testdb", "username"=>"user","password"=>"apass"]
		]
	],
]
```

An entry (in our example `"exc://exc.storage.db"` )  points to an array of optional parameters/configurations passed to the library.

To tell **EXC** where to look for a resource we use URL protocols. **EXC** supports the following special URLs:

 | Protocol | Description |
 | -- | -- |
 | `exc://` | A standard EXC module, located in `\exc\`. |
 | `app://` | An app specific module, located in your app folder. |
 | `file://` | A php include, the path relative to document root must be provided. |

#### Example loading an app library ####

Lets load the class `\emr\record` from the file `emr.record.php` located in our app folder.

```php
<?php
$options['using'] = [
	//example of a module in the app domain
    "app://emr.record" => [  //an app-specific module, "emr.record" is the namespace "\emr" and maps to the class "\emr\record"
        //exc will attempt to execute the static method initialize() from the class or from a class named manager if one is found.
        "enable_inmuno"=> true, //optional value-pairs passed to class::initialize($params) as an array in the $params argument
    ],

];
```

#### Example loading a php include/require ####

Lets load a php file named `myinclude.php` located in the folder `myapp/lib/`.

Paths are relative to your document root.

```php
<?php
$options['using'] = [
    "file://myapp/lib/myinclude.php" => [],
];
```

Since in this example the file is under your app folder you could also use the following to load `myinclude.php`:

```php
<?php
$options['using'] = [
    "app://lib/myinclude.php" => [],
];
```

#### Example loading composer ####

Lets load composer support for our app.

```php
<?php
$options['using'] = [
    "app://vendor/autoload.php"=>[],
];
```



#### Other examples ####

```
$options['using'] = [   //which modules to load
	"exc://exc.io.store"=> [
		"default_path" => "emr/records/patients/",
		"engines" => [
			[
				"engine"=>"MYSQL",
				"role"=>"master",
				"connection"=>["host"=>"127.0.0.1", "port"=>3366, "username"=>"exc.store","password"=>"webexc2013"]
			]
		]
	],
	"exc://exc.ui"=>[
		"views.paths"=>[
			'./views/',
			'../test02B/views/',
			'../test/views/'
		]
	],
	//example of a module in the app domain
	"app://emr.record" => [  //an app-specific module, "emr.record" is the namespace "\emr" and maps to the class "\emr\record"
		//exc will attempt to execute the static method initialize() for a class named manager if one is found.
		"enable_inmuno"=> true, //optional value-pairs passed to manager::initialize($params) as an array in the $params argument
	],

	//examples of hot to include a file
	//"app://test_include.php" => [], //will include this php from the app folder
	//"file://exc/test02A/test_include.php" => [], //will include a php in a particular path relative to doc root

	//load composer support
	//"app://vendor/autoload.php"=>[],

];
```
