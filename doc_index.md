**EXC** | DEV | [Documentation](./doc_index.md) | Version 1.0<BR>

## ExpressCODE ##

**ExpressCODE** (EXC) is a no frills, no fad, lets just get some work done framework to build web apps in JavaScript and PHP.

Creating web apps with EXC is meant to be simple, something that you can pickup and learn quickly. We put a lot of effort into making framework for modern web apps that is simple and lightweight.

EXC combines a client-side JavaScript framework with a backend framework in PHP, both loosely couple yet fully integrated.

## Our goals are: ##

To reduce and simplify the components that you have to code to make a web application.

To keep the learning curve low.

To reduce the security and operational complexity of deploying an application.

To leverage robust architectures and environments. To be able to deploy your app with common hosting companies (even vhosts), and use technologies like apache, and nginx, that are well documented, easy to scale and extremely cost-effective.    

> : This documentation is in its early stages. Things are thrown together in a haphazard way, Sorry!.

## Topics ##

[START-TOPICS]
[Getting Started]
[-] [Install and setup](./doc_app_structure.md)<BR> How to install EXC and setup an application.
[-] [Hello World!](./doc_getting_started.md)<BR> A simple tutorial to get you started.
[-] [Building the UI](./doc_server_ui_views.md)<BR> Basics of building your UI.
[-] [Working with views](./doc_client_view.md)<BR> How to use views in your app.
[-] [Working with the stage](./doc_client_stage.md)<BR> How to control views and user navigation in your app with the stage.
[-] [Interacting with the front-end](./doc_server_client.md)<BR> Leverage the backend to interact seamlessly with your front-end using the `$client`.
[-] [Working with sessions](./doc_session.md)<BR> Managing persistent data among requests and user interactions with your app.
[END-TOPICS]

[START-TOPICS]
[Front-End]
[-] [Controllers](./doc_client_controllers.md)<BR> Creating and using controllers in your javascript front-end.
[-] [The Controller's Manifest](./doc_client_controller_manifest.md)<BR> Adding dependencies of your controller with a manifest.
[-] [Views](./doc_client_view.md)<BR> How to use views in your app.
[-] [Stage](./doc_client_stage.md)<BR> How to control views and user navigation in your app with the stage.
[-] [HTTP and AJAX](./doc_core_ajax.md)<BR> Making HTTP requests and using ajax.
[-] [Components](./fte_ref_components.md)<br> Using components and widgets.
[-] [Expansions](./fte_ref_expansions.md)<br> Using magic attributes and other expansions.
[-] [Value Observers, Data Source and JSON API](./doc_datasource.md)<BR>
[END-TOPICS]


[START-TOPICS]
[Backend]
[-] [Controllers](./doc_server_controllers.md)<BR> Creating and using controllers in your php code.
[-] [Events and Messages](./doc_server_events.md)<BR> Understanding events and using event handlers.
[-] [Configuration options](./doc_server_config.md)<BR> How to start and configure an app.
[-] [Views](./doc_server_view.md)<BR> Creating views for pages, html fragments and components.
[-] [Database](./doc_server_db.md)<BR> Using a database and managed connections.
[-] [.htaccess](./doc_server_htaccess.md)<BR> Configuring APACHE `.htaccess` file.
[-] [Core Classes and Traits](./doc_server_core.md)<BR> Useful functionality provided by the core classes and traits.
[-] [Writing Extensions](./bke_ref_extensions.md)<BR> Creating and using extensions.
[-] [Class APP](./bke_ref_app.md)<BR> App class.
[-] [Class Controller](./bke_ref_controller.md)<BR> Controllers class.
[END-TOPICS]
