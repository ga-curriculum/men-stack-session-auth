<h1>
  <span class="headline">MEN Stack Session Auth</span>
  <span class="subhead">Protecting Routes with Middleware</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to develop and integrate custom middleware in an Express application to protect specific routes.

## The three parameters of custom middleware

When writing a custom middleware function, recall that we want three parameters instead of the usual two parameters our route handlers have been using:

**req** is the request object,
**res** is the response object,
**next** is the third parameter, representing the next function in the long line of middleware and route handlers that a request is processed through.

Unlike endpoint handlers, which typically use the response object `res` to send data back to the user, middleware functions are designed to perform a task and then proceed to the next step in the request-response cycle. This is achieved by calling the `next()` callback function.

Imagine we have a middleware function that we want to use in a specific context. For example, we can apply it to a route like `/vip-lounge`, ensuring that only authenticated users can access it:

```javascript
app.use(
  "/vip-lounge",
  (req, res, next) => {
    if (req.session.user) {
      res.locals.user = req.session.user; // Store user info for use in the next function
      next(); // Proceed to the next middleware or controller
    } else {
      res.redirect("/"); // Redirect unauthenticated users
    }
  },
  vipsController // The controller handling the '/vip-lounge' route
);

```

This approach works well when you need to apply this middleware logic to just one controller. However, if you need to use the same middleware across multiple controllers, this method might become repetitive. In such cases, we'd look for a more scalable way to integrate our middleware.

## Refactoring route protection with middleware

So far, we've protected our route by putting a simple `if` statement inside the route itself. You can imagine, however, that this would be repeated many times throughout our application, as many routes should be protected from unauthenticated or unauthorized users. Sounds like an opportunity to modularize and refactor- are we having fun yet?

Express's relatively non-opinionated nature means we'll probably want several custom-written middleware functions plugged in to our request pipeline. Let's anticipate this and create a whole directory dedicated to our custom middleware functions, and create our first route protection middleware inside this directory:

```bash
mkdir middleware
touch middleware/is-signed-in.js
```

We can otherwise move the same logic from our existing VIP Lounge route and put it into this middleware function.

In `middleware/is-signed-in.js`:

```javascript
const isSignedIn = (req, res, next) => {
  if (req.session.user) return next();
  res.redirect("/auth/sign-in");
};

module.exports = isSignedIn;
```

This function checks if `req.session.user` exists, and if it does, it allows the request to continue on the normal chain by invoking `next()` and returning. If this check fails, however, it moves to redirect the user to the sign-in page, strongly suggesting to the user that, to get where they want to go, they'll have to sign in.

An extreme bonus challenge for you might be to use session, or query parameters, to store the URL they were **trying** to get to, and changing the sign-in flow to redirect them back to that route once they've finished signing in. You may have seen this in URL bar of many real world applications: `?redirectURL=profile`, for example.

## Plugging in the Middleware

Now we can refactor our `vip-lounge` route to include this middleware before reaching the regular route handler.

First, we'll need to import the middleware function at the bottom of our growing list of `require` statements.

In `server.js`:

```javascript
const isSignedIn = require("./middleware/is-signed-in.js");
```

Interestingly, a route controller can accept any number of handler functions as inputs, so we can just add this function directly before the `(req, res)` callback.

Update `server.js` with the following:

```javascript
app.get("/vip-lounge", isSignedIn, (req, res) => {
  res.send(`Welcome to the party ${req.session.user.username}.`);
});
```

For future routes that require the user to be signed in, you can now simply import the middleware function we've created and plug it in the exact same way. Nice!

<!-- [Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/protecting-routes-with-middleware-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/protecting-routes-with-middleware-complete) -->
