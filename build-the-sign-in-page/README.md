# ![Mongoose, Express, EJS, and Session Auth - Build the Sign In Page](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a page for users to sign in to an express application.

## Creating the Sign In Page

Now that users can sign up for the app, it's time to give them a form so they can sign **in** to the application. This template will be very similar to the sign up page, with only the `confirmPassword` field being removed, and the `action` directed towards a different url.

```bash
touch views/auth/sign-in.ejs
```

In `views/auth/sign-in.ejs`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sign In</title>
</head>
<body>
  <h1>Sign in</h1>
  <form action="/auth/sign-in" method="POST">
    <label for="username">Username:</label>
    <input type="text" name="username" id="username" />
    <label for="password">Password:</label>
    <input type="password" name="password" id="password" />
    <button type="submit">Sign in</button>
  </form>
</body>
</html>
```

## Adding a Navigation Link to the Sign In Page

Like we did for the sign up page, let's also add a link on our landing page to the sign in page:

In `views/index.ejs`:

```html
<a href="/auth/sign-in">sign in</a>
```

## Creating the Route

Finally, we can add a simple route to our controller file to render this template.

In `controllers/auth.js`:

```javascript
router.get('/sign-in', (req, res) => {
  res.render('auth/sign-in.ejs');
});
```

Test the route by navigating from the landing page link.


[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-the-sign-in-page-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-the-sign-in-page-complete)
