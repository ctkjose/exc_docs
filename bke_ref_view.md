[Core Classes]() {menu:}
[app](./bke_ref_app.md) {menu:}
[appController](./bke_ref_appcontroller.md) {menu:}
[view](./bke_ref_view.md) {menu:}
[controller](./bke_ref_controller.md) {menu:}

**EXC** | DEV | [Documentation](./doc_index.md)<BR>

# Class exc\view #

### Class Methods ###

```
public static function load($name='default')
```

Factory method to create an instance of `view` by name.

```
public static function createWithFile($path, $name='default')
```

Factory method to create an instance of `view` from a file.

```
public static function createWithSource($source, $name='default')
```
Factory method to create an instance of `view` from a $source string containing HTML.

```
public static function getViewPathForName($name)
```

Returns a string with the absolute path for a view file given its name. Returns

```
public static function constantSetValue($key, $value)
public static function constantSetValue($key1, $value1, $key2, $value2...)
public static function constantSetValue($anArrayOfValuePairs)
```
Sets the value of a **@CONTANT** placeholder.

```
public static function constantCopyValues($anArrayOfValuePairs, $prefix='')
```
Creates a **@CONTANT** placeholder for each key in an array of value-pairs.


### Object Methods ###

```
$viewInstance->placeholderName->set($html)
```
Call the `set($HTML)` method on a placeholder to sets its contents.

```
$viewInstance->placeholderName->write($html)
```

Call the `write($HTML)` method on a placeholder to append to its contents.

```
$viewInstance->js->src($urlToJS)
$viewInstance->css->src($urlToCSS)
$viewInstance->css->src($urlToCSS, $mediaType)
```

Call the helper method `src()` on the `js` or `css` placeholders to create a script or style tag.


```
$viewInstance->getHTML()
```
Renders the view and returns the compiled HTML code.

```
$viewInstance->initializeSource($srcHTML)
```
Initialize a view with its HTML source.
