**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Architecture: App Life-Cycle#


## bootloader::run ##

1. The `app` options are added to the `options` class.

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
