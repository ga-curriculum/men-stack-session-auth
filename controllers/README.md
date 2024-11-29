<h1>
  <span class="headline">MEN Stack Session Auth</span>
  <span class="subhead">Controllers</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create and integrate a controller for auth routes in an Express server.

## Create controllers

Since we're building an authentication app, we should anticipate the need for multiple models in the future. After all, users will want to create and manage resources, not just sign in and sign out of an otherwise useless application.

So, this means splitting off the routes for authentication into a separate file, so we can have a clean distinction between routes for auth and routes for other models being managed by users. This allows our authentication app to be a highly re-usable, modular code base that provides a starting point for any number of future applications!

Let's start by making a `controllers` directory and `auth.js` file for the functions that handle incoming requests:

```bash
mkdir controllers
touch controllers/auth.js
```

This file will look similar to the way we set up routes in `server.js`, with one key exception: an express built-in `router` object will replace the `app` object, and we can plug in that `router` object later in `server.js`.

Add the following to `controllers/auth.js`:

```javascript
const express = require("express");
const router = express.Router();

module.exports = router;
```

Now, let's import the `authController`, which contains our `router` object, into `server.js`. This should be done right after we declare the `port` variable:

```javascript
const authController = require("./controllers/auth.js");
```

After importing, we'll instruct our Express app to use this `authController` for handling requests that match the `/auth` URL pattern. 

To do this, add the following line in `server.js`, just below where we've defined our home page route:

```javascript
app.use("/auth", authController);
```

With this code in place, Express will now funnel any requests starting with `/auth` to the `authController`. The `authController` is essentially a set of routes defined in `auth.js`, managed by the `router` object. 

For each of these routes, the URL path handled by the `authController` will already have `/auth` as its base. Therefore, within the `auth.js` file, we only need to specify the remaining part of the URL path, excluding `/auth`, for each route.

For example, the controller route for `/auth/sign-up` in the `authController` will look like this:

```javascript
router.get("/sign-up", (req, res) => {
  res.render("auth/sign-up.ejs");
});
```

The full URL to access that route from a browser would be `/auth/sign-up`.

<!-- [Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/controllers-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/controllers-complete) -->
