**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Backend Application Configuration #
*This topic applies to the BackEnd framework.*

Configuration options are provided in the **config** massage of your [appController](./doc_server_appcontroller.md).

```php
<?php
class appController extends \exc\controller\appController {
	public function config(){
		//place configuration options here
		$options = [];
		...
		return $options;
	}
}
```

The `config()` function returns an array with the configuration options that you want to set. This array has keys for configuration directives, in essence an array of key-value pairs.

## Adding your own configuration keys ##

Besides the configuration options defined by EXC, you can also use the `$options` array to add your own configuration keys. For example:
```php
<?php
class appController extends \exc\controller\appController {
	public function config(){
		//place configuration options here
		$options = [];
		$options["templateName"] = "summer2019"; //a user configuration
		return $options;
	}
}
```

To read the value of your key you use the following:
```php
<?php
//get the value of a key
$cfgTemplateName = \exc\options::key("/app/templateName");

```


## Route Option ##

The **route** allows to set configuration option for a particular action.

For example let say we have an action "employee.showRecord" (that is the action "showRecord" of your "employeeController"), then we can add configurations options that apply only for that particular action.

Lets see the example:

```php
<?php
class appController extends \exc\controller\appController {
	public function config(){
		//place configuration options here
		$options = [];
		$options['route'] = [
			'employee.showRecord' => [  //options for this action
				'view.copy'=> [ //add files to the view output
					['type'=>'js', "url"=>'app://assets/js/tests.js'], //insert js file into the view
				]
			]
		]
		return $options;
	}
}
```

In this example we added a `view.copy` configuration directive to the "employee.showRecord" action. That `view.copy` directive will only apply when that action is executed. In EXC we call that a scoped configuration.


## CONTROLLERS Option ##

The option key `controllers` is where we list any controller that you may need to be loaded on every interaction with the backend.

```PHP
<?php
//controllers that are installed
$options['controllers']=[
	"recordController" => "app://controller.record.php",
];
```

> * TIP: You may also use the **controllers** directive inside your **route** options.

## VIEW.COPY Option ##

In `view.copy` you can specify a list of javascript files, css and others that you want included in a given view.

The format of this option is:
```js
	$options['view.copy'] = [
		['*'] => [
			["type"=>"js", "url"=>'app://assets/js/tests.js'], //global code
		]
	];
```
The `view.copy` is a **scoped** key meaning that you specify entries that apply to a given action of for every action. In the above example we use the wildcard scope `"*"` to always add these items regardless of the action.
```js
	$options['view.copy'] = [
		['*'] => [
			["type"=>"js", "url"=>'app://assets/js/tests.js'], //global code
		],
		['app.showprofile'] => [
			["type"=>"js", "url"=>'app://assets/js/profile.js'], //global code
		],
	];
```
In this example we added the scope `'app.showprofile'`, the items under this entry will be included only for this particular action.

Each entry is a `hash` with the following keys.

| Key | Description |
| -- | -- |
| url | **Required**: A string with the url of the resource.You may use EXC special URL protocols. |
| type | A string that specify a special handling for the resource. Valid values are: 'js', 'css' |


## USE Option ##

The option key `use` is where we add libraries and modules for your backend.

Each entry in `$options['use']` points to a resource that you want to load. For example lets load the library `exc.storage.db`:

```js
$options['use'] = [   //which modules to load
	"exc://storage.db"=>[
		"connections"=>[
			"test1" => ["driver"=>"mysql", "host"=>"192.168.100.175", "port"=>3306, "dbname"=>"testdb", "username"=>"user","password"=>"apass"]
		]
	],
]
```

An entry (in our example `"exc://storage.db"` )  points to an array of optional parameters/configurations passed to the library.

#### Example loading an app library ####

Lets load the class `\emr\record` from the file `emr.record.php` located in our app folder.

```php
<?php
$options['use'] = [
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
$options['use'] = [
    "file://myapp/lib/myinclude.php" => [],
];
```

Since in this example the file is under your app folder you could also use the following to load `myinclude.php`:

```php
<?php
$options['use'] = [
    "app://lib/myinclude.php" => [],
];
```

#### Example loading composer ####

Lets load composer support for our app.

```php
<?php
$options['use'] = [
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
