<h1>
  <span class="headline">MEN Stack Session Auth</span>
  <span class="subhead">Make the <code>User</code> Session Available to All Views</span>
</h1>

**Learning objective:** By the end of this lesson, students will have the skills to make user session information universally accessible to all views in an Express application

## The magic of `res.locals`

In our web application, it's not just the protected routes that might need access to the signed-in user's information. Consider a common feature like a navbar, which typically changes its display based on the user's authentication status – showing a 'sign-in' button for guests and a 'sign-out' button for logged-in users. To make this work, **every template** in our application needs access to the user information stored in the session.

Now, we could add the user information to the context object of every `res.render` call throughout the app. However, this approach is not very efficient or maintainable. It's not very DRY and prone to errors, especially in a large application where you might miss adding this information in some routes.

A more elegant solution in Express is to use the `res.locals` object. This object is part of every request in Express and is specifically designed for situations like ours. Any property added to `res.locals` becomes automatically available to all rendered templates. This means we can set user information once in `res.locals`, and it will be accessible in every template without the need to repeatedly pass it in the render function.

Here's how it works in practice:

```javascript
res.locals.magicNumber = 13;
res.render("/some-template.ejs");
```

In this example, `magicNumber` is now available in `some-template.ejs`:

```html
<p>The magic number is <%= magicNumber %></p>
```

Similarly, we can set the signed-in user's information in `res.locals` to make it universally accessible across all templates, simplifying our code and reducing the risk of inconsistencies.

## Creating our middleware

Let's add a new file to our `middleware` directory:

```bash
touch middleware/pass-user-to-view.js
```

This file will contain a middleware function that assigns the user information from the session to `res.locals`. By doing this, we ensure that the `user` property is available in all templates that are rendered after this middleware has been executed.

In `middleware/pass-user-to-view.js`:

```javascript
const passUserToView = (req, res, next) => {
  res.locals.user = req.session.user ? req.session.user : null;
  next();
};

module.exports = passUserToView;
```

If `req.session.user` exists (meaning the user is signed in), we assign this value to `res.locals.user`. If `req.session.user` is not present (the user isn't signed in), we set `res.locals.user` to null.

This logic could also be implemented with an `if...else` statement, but the ternary operator offers a more streamlined approach. The key takeaway is that regardless of the method used, we’re ensuring that every template can check the `res.locals.user` variable to determine the user's sign-in status.

## Plugging in our middleware

First, we need to add the import statement to our stack of `require` statements in `server.js`:

```javascript
const passUserToView = require("./middleware/pass-user-to-view.js");
```

Next, we need to think strategically about where to place this middleware within the request processing pipeline. Since our aim is to make the `user` variable available to all view routes, it's crucial to insert this middleware early in the route chain. However, it's also important that it comes after our `session` middleware, as it relies on session data.

With this in mind, we should add our `passUserToView` middleware immediately after the session middleware in `server.js`. This ensures that every request has access to the user session data before reaching any route endpoints.

Here's how we add it to the middleware stack:

```javascript
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true,
  })
);
// Add our custom middleware right after the session middleware
app.use(passUserToView);
```

## Refactoring the Landing page route

If this middleware works properly, it means no other route should ever need to add `req.session.user` to the context object being provided to a render statement. Let's test this out by removing the context object from our landing page route, and simplifying it back down to the following:

```javascript
app.get("/", (req, res) => {
  res.render("index.ejs");
});
```

The user should still show up on this page thanks to our middleware! If you want to create a standard navbar with sign-in/sign-out buttons, now would be the time to do so. All templates will have access to a `user` variable you can create conditionals around.

<!-- [Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-complete) -->
