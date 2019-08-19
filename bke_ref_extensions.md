**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Extensions #

Extensions provide the means to easily add functionality to an application.

## File Structure ##

Each extension must be in its own folder. The folder must be placed inside EXC's "extensions" folder.

The name of the folder is a unique extension identifier in lowercase.

The name of an extension must use only letters and the underscore.

The folder must contain a main php file responsible of loading your extension. This file must be named as follows:

"ext." + "name" + ".php"

For example if the extension name is "sso" then a file named "ext.sso.php" must be found inside your extension folder.

## Writing an extension ##

Each extension must use a namespace, you may use `namespace ext\myname;`.

By default the following variables are available:

| Variable | Description |
| -- | -- |
| $app | An instance of your AppController. |
| $exc_extension_dir | A string with the path to the current extension folder. |
| $exc_extension_name | A string with the name of your extension. |

## Using classes ##

We recommend using classes in your extension. A good approach to initialize your extension is to write a "manager" class and call a static initialization function.

```php
<?php
namespace ext\myname;
class manager extends \exc\base {
	public static function initialize(){
		//init code here...
	}
}

manager::initialize();
```

## Using closures ##

A good way to write your init code is using a closures:

```php
<?php
(function($app){
	error_log('Initializing my extension');

	//check compatibility
	if (version_compare(EXC_VERSION, '1.0.5') < 0) {
		error_log('EXC:ERROR:EXTENSION AUTH REQUIRES EXC VERSION 1.0.0 or greater');
		return;
	}

	//my init code ....

})($app);
```


## Options ##

Options for an extension are set in your `config()` function of your `appController`.

```php
<?php
$options['extension.myname'] = [
	'key1'=> "value1",
];
```

To fetch your options in your extension use:

```php
<?php
//my required and default values
$default = [  
	'key1'=> 'value1',
	'key2'=> 'value2'
];

$options = \exc\options::key('/app/extension.myname');
///\exc\error_log_dump($options);

//ensure options have my default and required values
$ops = \exc\base::extend($default, $options);
\exc\error_log_dump($ops);

```
