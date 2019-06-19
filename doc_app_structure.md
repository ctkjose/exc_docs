**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

# Overview #

A common application in EXC is made up of two parts a front-end and a back-end.

In web-development we usually talk about the front-end as the aspects of the application that your user sees and interacts with. The front-end runs in your browser and is build on top of HTML, JS and CSS.

Since in EXC an application is a single page javascript application ([SPA](https://en.wikipedia.org/wiki/Single-page_application)),
a lot of the logic and workflow of your application lives in your front-end. Just a clarification: Truth be said EXC allows you to do more traditional multipage applications, what makes it a SPA is how you decide to serve your content.

The front-end framework is build to handle your UI, the user interactions and navigation. Its meant to make the whole process of building a modern web-application quite simple.

The back-end is the facilitator of your front-end, is the grunt doing anything needed, but mainly is our place where we keep the sensitive logic secure.

In other frameworks the interaction of your front-end and back-end is glued by web-services. You use web-services to get data, add and update records, etc. Building good and secure web-services is not a simple task and is very common to find a web app exposing all our their data and actions with poorly design web-services.

The difference in EXC is that our back-end is an actual API of controllers and functions that works seamlessly and transparently with your front-end without the need of scaffolding a whole set of web-services just for your application. In the back-end you can leverage the versatility of PHP to access databases, connect to web-services or integrate with other systems as needed.

# Setting up the environment #

An EXC app can be deployed in different ways but the easiest is to serve your applications using APACHE or NGINX.

This guide will cover a basic setup for EXC using APACHE, but by no means is the only way to deploy EXC. People with a bit more knowledge on APACHE and NGINX will have no problem on setting up other settings, in particular to deploy EXC so it can be shared with many applications in the same hosting. We also limit this guide to things that can be done in your average web hosting.

For the purpose of this guide we assume that you already have a traditional APACHE/PHP environment ready. If you have a Mac you already have pretty much everything you need to start developing and testing your application right on your laptop, else you can google one of the many guides to set up a traditional PHP environment like LAMP.

Getting your app to work on your deployment server and your web hosting server (production) is pretty much the same, unless indicated otherwise a step on this guide applies to both setups.

Like when hosting a traditional web-page, your app is serve by APACHE or NGINX. Your basic app is just another folder in your [DOCUMENT-ROOT](http://httpd.apache.org/docs/2.4/mod/core.html#documentroot). This folder holds your front-end and back-end code and other resources like css, etc.

## Installing the EXC framework ##

The framework comes in two parts the [front-end](https://github.com/ctkjose/exc_core/) and the [back-end](https://github.com/ctkjose/exc_server/), the relevant code for each of part is inside a folder called `exc`. Installing the framework is basically copying the code to your server.

You can deploy the framework inside you app folder.  

> In a bit more advance setup we suggest you place it directly on your [DOCUMENT-ROOT](http://httpd.apache.org/docs/2.4/mod/core.html#documentroot) that way it is installed once and share it with other apps.

Download the `exc_server` code from our [git-hub repository](https://github.com/ctkjose/exc_server/). Copy the `exc` folder to your app folder.


> : Depending were you place the framework you may need to change the references to css and js files from your html. We will discuss this in more detail later.


The back-end framework uses an [.htaccess](https://httpd.apache.org/docs/2.4/howto/htaccess.html) file to add a some [rewrite](https://httpd.apache.org/docs/2.4/rewrite/intro.html) rules.

> ? **MORE:** Read the [documentation](./doc_server_htaccess.md) for more details about `.htaccess` and rewrite rules in EXC.

Create a file named `.htaccess` in your app folder with the following contents:
```
RewriteEngine On

RewriteRule  ^((?:[^\/]*/)*)(c\/)((?:[^\/]*\/)*)([A-Za-z0-9-_]+\.[A-Za-z0-9-_]+|[A-Za-z0-9-_]+)$  exc/loader.php [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(o\/)((?:[^\/]*\/)*)([A-Za-z0-9-_]+\.[a-z]{2,3})$  exc/loader.php [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.js)$  $1/assets/js/$3 [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.css)$  $1/assets/css/$3 [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(a\/)((?:[^\/]*\/)*)([A-Za-z0-9-_\.]+\.[a-z]{2,3})$  $1/assets/$3$4 [L,QSA]
RewriteRule  ^((?:[^\/]*/)*)(a\/)([A-Za-z0-9-_\.]+\.[a-z]{2,3})$  $1/assets/$3 [L,QSA]
```



These rules allows you to use urls like:
| Short hand | Equivalent path |
| -- | -- |
| /my/path/a/script.js | /my/path/assets/js/script.js |
| /my/path/a/style.css | /my/path/assets/css/style.css |
| /my/path/a/myfolder/afile.css | /my/path/assets/myfolder/afile.css |


Every app needs an `app.php` file which tells EXC about your app. Inside `app.php` we use the `$options` array to configure our app.

```php
<?php

$options['uid'] = "ACMEEMP"; //our app identifier, every app needs an identifier...

//add some controllers controllers
$options['controllers']=[
	"record" => "./controller.record.php",
	"findEmployeeController" => "./controller.findEmployee.php",
];

?>
```
