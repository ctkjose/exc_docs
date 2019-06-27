**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# CONSTANTS #

| Constant | Description |
| EXC_RUNMODE | Integer, Values: 1 = SERVER/SAPI/WEB, 2= CLI |
| EXC_DOCUMENT_ROOT | Absolute path of document root when running under SAPI or current working directory under CLI. |
| EXC_PATH | Absolute path to EXC's framework code. |
| EXC_PATH_BASE | The absolute path to the resource specify in the URL. Equivalent to `\exc\bootloader::$route['base_path']`. |
| EXC_PATH_APP | The absolute path to the application. Also available in `\exc\options::key('/app/path/base')`. |
