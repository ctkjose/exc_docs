
# App Loading #
1. `exc.app.waitForAppInstance()` polls the global scope for an `app` object.
2. `exc.app.loadController()` load the app object found.
3. `exc.app.processController()` is called after items on the manifest are loaded.
