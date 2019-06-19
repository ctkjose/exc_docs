**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# .htaccess File #
*This topic applies to the BackEnd framework.*




# Rewrite Rules #

The rules for short-hand urls included in the `.htaccess` are mainly for scenarios where you have one instance of EXC that is acting as an application server. In a more typical setting of stand-alone applications these rule may not be considered that much useful, but we urge users to keep them on their deployments.

## Short-Hand for the javascript and css folder ##

```
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.js)$  $1/assets/js/$3 [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.css)$  $1/assets/css/$3 [L,QSA]
```

These rules allows to access files in your application's `assets/js/` and `assets/css/` by just using the shorthand `myapp/a/script.js` or `myapp/a/style.css` which will map to your corresponding `js` or `css` folder.

## Short-Hand for the assets folder ##

```
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.[a-z]{2,3})$  $1/assets/$3 [L,QSA]
```
This rules allows you to refeer to you assets folder just using `a/`, for example `myapp/a/myfolder/afile.css` will be `myapp/assets/myfolder/afile.css`.


## Controller Mapping ##

```
RewriteRule  ^((?:[^\/]*/)*)(c\/)((?:[^\/]*\/)*)([A-Za-z0-9-_]+\.[A-Za-z0-9-_]+|[A-Za-z0-9-_]+)$  exc/loader.php [L,QSA]
```

This rule adds what we call a controller URL.
