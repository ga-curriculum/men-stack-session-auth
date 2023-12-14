# ![Mongoose, Express, EJS, and Session Auth - Controllers](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a controller file to import routes into an express server file.

## Refactoring Controllers

Since we're building an authentication app, we should anticipate the need for multiple models in the future. After all, users will want to create and manage resources, not just sign in and sign out of an otherwise useless application. 

So, this means splitting off the routes for authentication into a separate file, so we can have a clean distinction between routes for auth and routes for other models being managed by users. This allows our authentication app to be a highly re-usable, modular code base that provides a starting point for any number of future applications!

Let's start by making a directory and file for the controller functions that handle incoming requests:

```bash
mkdir controllers
touch controllers/auth.js
```

This file will look similar to the way we set up routes in `server.js`, with one key exception: an express built-in `router` object will replace the `app` object, and we can plug in that `router` object later in `server.js`.

In `controllers/auth.js`:

```javascript
const express = require('express');
const router = express.Router();

module.exports = router;
```

Now, we need to import this `router` object into `server.js`, right after the line declaring the `port` variable:

```javascript
const authController = require('./controllers/auth.js');
```

In `server.js` beneath the home page route:

```javascript
app.use('/auth', authController);
```

This code processes all incoming requests, and if the url starts with `/auth`, it sends the request to be completed in the `authController.js` file by the `router` object. From that point on, the controller only needs to finish the route's url **after** `/auth`, and we won't need to include `/auth` in the controller file's routes. 

For example, the controller route for `/auth/sign-up` will look like this:

```javascript
router.get('/sign-up', (req, res) => {
  res.render('sign-up.ejs');
});
```

The full url to access that route will be `/auth/sign-up`.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/controllers-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/controllers-complete)
