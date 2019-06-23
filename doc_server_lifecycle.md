**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Architecture: App Life-Cycle#


1. Set constant `EXC_RUNMODE` which identifies if it is running in [CLI or SAPI](https://www.binarytides.com/php-check-running-cli/) (Server API) (eg: APACHE, NGNIX, CGI, FCGI, etc). This is done with:

```php
if( defined('STDIN') || in_array(PHP_SAPI, ['cli', 'cli-server', 'phpdbg'], TRUE) ) {
	define('EXC_RUNMODE', 2); //is CLI
}else{
	define('EXC_RUNMODE', 1); //is SAPI, WEB
}
```

2. If a file named `exc.config.php` is found in the `exc` folder it will be included.

```
define('EXC_PATH_APPS_FOLDER', dirname(__DIR__) . "/"); //location of our apps
```

## bootloader::run ##

1. If `EXC_RUNMODE == 1` we initialize the `bootloader` from a SAPI, by calling `initFromHTTP()`.

2. In `initFromHTTP()` we:

2.1. Set the constant `EXC_DOCUMENT_ROOT`.

2.2. Find if we where called with a controller URL using a rewrite rule in our `.htaccess`.

2.2.1. Find the path to the folder of the desired URL and set `\exc\bootloader::$route['base_path']`.

2.2.2. We get the controller name and an action and set `\exc\bootloader::$route['controller_name']` and `\exc\bootloader::$route['action']`.

2.3. Load values from `$_REQUEST` and from `api_json_data`.

2.4. We set the `self::$route['action_type']` to either `runController`, `runInclude`, `runPassthru`.

3. If the action is `runController` the function `\exc\bootloader::runController()` is executed.

4. In `runController()` we:

4.1. We invoke `\exc\app::loadApp()` to load the app and create a `appController` by looking for a `controller.app.php` file or creating a generic instance.

4.1.1. The `controller.app.php` was loaded, the `appController->config()` was called.

4.2. We load any particular controller requested in `$route['controller_name']` and set it as FirstResponder.

4.3. If in SAPI we load the `session`.

4.4. We relinquish execution to `\exc\app::runWithAction(self::$route['action']);`.







## Options ##

The class `\exc\options` is a key-value store of configurations, indicators and other special values.

Use the function `\exc\options::key($name)` to read a value, or `\exc\options::key($name, $value)` to set a key value.

| Key | Description |
| -- | -- |
| \app\uid | Unique identifier. |
| \app\firstresponder | Name of class acting as First-Responder. |
| \app\path_app | An absolute posix path to the directory of the application. |
| \app\path_views | An absolute posix path to the directory in your application folder with views. |
| \app\paths\base | An absolute posix path to the directory from where you are serving the applications. |
| \app\paths\controller | An absolute posix path to the controller file invoked. |
| \app\paths\controller_directory | An absolute posix path to the parent folder of the controller invoked. |
| \app\paths\resource |  A relative path to the parent folder of the request. |
| \app\urls\base | The root portion of the URL pointing to the directory serving the application. |
| \app\urls\assets_directory | The root portion of the URL pointing to the assets folder. |
| \app\urls\controller_directory | The root portion of the URL pointing to the controller folder. |
