# ![Mongoose, Express, EJS, and Session Auth - Level Up - Making the User Session Available to All Views](./assets/hero.png)

## The Magic of res.locals

In addition to our route protection middleware, many other templates will want access to the signed in user. In fact, if you want the typical navbar that shows users a `sign-in` button if they're not authenticated, and a `sign-out` button if they are, then you'll need **every template** to have access to the signed-in user stored in session.

Sure, we could simply add to the context object provided to every `res.render` statement in the entire app, but that doesn't sound very DRY. It sounds like an invitation for bugs when you forget to add it, or add it differently, in one route out of 100.

Therefore, we're going to make use of the almost magical `res.locals` object that express attaches to every single request in the pipeline. This object allows you to directly add new properties that will be available to the rendered template, under the same name. For example, if a route handler sets the following property:

```javascript
res.locals.magicNumber = 13;
res.render('/some-template.ejs');
```

Then the rendered template will have access to a `magicNumber` variable like so:

```html
<p> The magic number is <%= magicNumber %>
```

## Creating Our Middleware

Let's add a new file to our `middleware` directory:

```bash
touch middleware/pass-user-to-view.js
```

In this file, we can add our `res.locals` assignment to provide a `user` property to all templates rendered after this middleware is executed.

In `middleware/pass-user-to-view.js`:

```javascript
const passUserToView = (req, res, next) => {
  res.locals.user = req.session.user ? req.session.user : null;
  next();
}

module.exports = passUserToView
```

While we're using a ternary to shorten up the code, this could have been done with an `if...else` statement exactly the same. The logic remains the same: if there's a `user` property in `req.session`, send it to the template. If there isn't, set the `res.locals.user` property to `null`, so that all templates have a variable they can test to see if the user is signed in or not.

## Plugging In Our Middleware

Now, since all view routes are going to want access to this variable, we should place this middleware in the chain before any possible endpoints have been reached. However, we also need our session middleware to be set up before this middleware makes sense, so we can place it just underneath the session middleware.

First, of course, we need to add the import statement to our stack of `require` statements:

```javascript
const passUserToView = require('./middleware/pass-user-to-view.js');
```

Then, we can add this to our middleware stack in `server.js`:

```javascript
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true,
  })
);
// NEW LINE ADDING OUR MIDDLEWARE TO THE REQUEST PIPELINE
app.use(passUserToView);
```

## Refactoring the Landing Page Route

If this middleware works properly, it means no other route should ever need to add `req.session.user` to the context object being provided to a render statement. Let's test this out by removing the context object from our landing page route, and simplifying it back down to the following:

```javascript
app.get('/', (req, res) => {
  res.render('index.ejs');
});
```

The user should still show up on this page thanks to our middleware! If you want to create the standard navbar with sign-in/sign-out buttons, now would be the time to do so. All templates will have access to a `user` variable you can create conditionals around.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-complete)
