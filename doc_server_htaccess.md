**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Protecting your php code #

It is a good idea to isolate your code so that only the absolute minimum is reachable via traditional http.

In EXC we recommend that you place your code in a folder named `src`. To protect this folder you just need to add a [.htaccess](https://httpd.apache.org/docs/2.4/howto/htaccess.html) file with the following line:

```
deny from all
```

This prevent users to make direct http request to your files.

# .htaccess File #

EXC uses an [.htaccess](https://httpd.apache.org/docs/2.4/howto/htaccess.html) file under APACHE to add some rewrite rules and add some security options.

The following is an explanation of options recommended for your `.htaccess`.

## Security entries ##

```
Options -Indexes
```

Disable directory listing

## Lock down files ##
```
<files app.php>
	order allow,deny
	deny from all
</files>
<files controller.*.php>
	order allow,deny
	deny from all
</files>
```

Disable access to special files, request for these files will get a HTTP 403 Forbidden error.


## Rewrite Rules ##

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
RewriteRule  ^((?:[^\/]*/)*)(c\/)([A-Za-z0-9-_]+\.[A-Za-z0-9-_]+|[A-Za-z0-9-_]+)$  exc/loader.php [L,QSA]
```

This rule adds what we call a [controller](./doc_server_controllers.md) URL. A controller url look like this:

`/myapp/c/main` or `/myapp/c/main.loadRecord`

In the example above the part after `/c/` is what we call the controller segment. It tells EXC that you are invoking a given action in your controller. In the first example the controller is `main` and it does not specify an action. The second example also calls the controller `main` but it specifies the action `loadRecord`. A controller url is send to `/exc/loader.php` to dispatch the request.

In this rule we have to ensure that we have a correct path to `exc/loader.php` depending on your setup. As provided in this example it would look for the folder `exc` in the same folder that you have your `.htaccess` file.

In a setup where EXC is a shared instance we may want to place this rewrite rule in a `.htaccess` on the same folder where you put the shared instance of `exc`. This will allow for some handy urls, for example lets say we put our `exc` folder to be shared in the folder `apps` of our document root, then we can do:

`https://mydomain/apps/myapp/c/main.start`

## JWT Support ##

```
RewriteRule .* - [env=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
```

Enable JWT support by copying any Authorization header to an environment variable.


# Optional entries #

```
RewriteCond %{HTTP_REFERER} !^http(s)?://(www\.)?example.com [NC,OR]
RewriteRule \.(jpeg|JPEG|jpe|JPE|jpg|JPG|gif|GIF|png|PNG|mng|MNG|svg|SVG)$ - [F]
```

Prevent image hot linking, explained [here](https://help.dreamhost.com/hc/en-us/articles/216363197-How-do-I-prevent-image-hotlinking-).
