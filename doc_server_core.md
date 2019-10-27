[Related](){:menu}
[Back-end Framework](./bke_index.md){:menu}
**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Base Object #
**Class:** exc\core\base<br>
Most classes in EXC extend the `\exc\core\base` class.

### Instance Methods ###

| Function | Description |
| -- | -- |
| `getOwnPropertyNames()` | Returns an array with the names of the properties in this object. |
| `hasOwnProperty($propName)` | Returns true if the object has a property named `$propName`. |
| `performMessage($messageName, $paramsArray)` | Executes the function or handler corresponding to $messageName. (See Events handlers and performMessages) |
| `getMessageHandler($messageName)` | Returns a callback compatible with [`call_user_func_array()`](https://www.php.net/manual/en/function.call-user-func-array.php) for a function or handler corresponding to $messageName. If none is available it will return `null`. |
| `isInstanceOf($class)` | Returns true if the object inherits the `$class`. |

### Class Methods ###

| Function | Description |
| -- | -- |
| `isBaseInstance($object)` | Returns true if the `$object` inherits from `exc\core\base`. |
| `extend($objectA, $objectB, ... )` | Copies the object properties of `$objectB` to `$objectA`. You may pass more objects as arguments to copy their properties. |

## Dynamically adding functions to an object ##

An object that inherits from `\exc\core\base` can be extended by using [Closures](https://www.php.net/manual/en/functions.anonymous.php).

```PHP
<?php
class myClass extends \exc\core\base {

}

$obj = new myClass();
$obj->doThis = function(){
	error_log("inside an anonymous function");
};

$obj->doThis();

```

## Events handlers and performMessage() ##

In EXC an event handler is a function, who's name follows a simple naming convention (an event handler signature). A function name that starts with `"on"` , for example `onAppInit` is considered a handler for the event `"AppInit"`.

The method `performMessage($messageName, $paramsArray)` allows you to safely execute methods or an event handler implemented by the object that inherits from `\exc\core\base`.

| Parameter | Description |
| -- | -- |
| $messageName | A string with the name of the message or function name. |
| $paramsArray | The parameters to be passed to the callback, as an indexed array. |

If the object does not implements the function, then the `unhandled($messageName, $paramsArray)` function will be executed giving you one last chance to handle it.

When calling `performMessage()` it will first look for a function that matches `$messageName`, if found it will be executed. Then it will look for an event handler in the object that matches the name given in the parameter `$messageName`. Finally it will look for a `closure` function.

Lets see an example of an event handler:

```PHP
	<?php
	class myClass extends \exc\core\base {
		public function onDoThis(){
			error_log("executed doThis event handler");
		}
	}

	$obj->performMessage("dothis");
```



# Extendable Objects #
**Trait:** \exc\core\objectExtendable<br>
The **trait** `\exc\core\objectExtendable` allows you to add methods to an object dynamically at runtime using php anonymous functions.

```php
<?php
class myClass {
	use \exc\core\objectExtendable;
}

$o = new myClass();

//add a method to our object
$o->addMethod('doThis', function(){
	error_log("called do this...");
});

$o->doThis(); //lets call our new method...

?>
```

### Using delegation with objectExtendable ###
An object that uses the trait `\exc\core\objectExtendable` has two helper functions to obtain and set delegates.

The method `delegateFor($name)` will return an anonymous function for an object's method. The anonymous function returned still executes under the scope of the owner object. Things like `$this` will still work and would point to the owner instance.  

The method `delegate($name, $fn)` allows you to set or add a method to an object that uses objectExtendable.

```php
<?php
class myClass {
	use \exc\core\objectExtendable;
}
class myHelperClass {
	use \exc\core\objectExtendable;
	public $name = '';
	public function doThis(){
		//$this points to the instance of myHelperClass
		$this->setName('Joe');
		error_log("Name is " . $this->name);
	}
	public function setName($s){
		$this->name = $s;
	}
}

$a = new myClass();
$b = new myHelperClass();

//add the method doSomething() to $a, that will be implemente by $b->doThis()
$a->delegate("doSomething", $b->delegateFor('doThis'));

$a->doSomething(); //lets call our new method...

?>
```
