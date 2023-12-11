# ![Mongoose, Express, EJS, and Session Auth - Sign the User Out](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement user sign out in an express application.

## Signing the User Out

Now that we're creating sessions when users sign in, we can simply delete those sessions when a user wants to sign out.

Let's start by adding a link on the landing page for users to sign out. We only want to show this link if there is a currently signed in user, so we should put it inside the same conditional that displays that user's username.

In `views/index.ejs`:

```html
  <% if (user) { %>
    <h1>Welcome to the app, <%= user.username %>!</h1>
    <p>
      <a href="/auth/sign-out">Sign out</a>
    </p>
  <% } else { %>
    <h1>Welcome to the app, guest.</h1>
    <p>
      <a href="/auth/sign-up">Sign up</a> or
      <a href="/auth/sign-in">Sign in</a>.
    </p>
  <% } %>
```

## The Sign Out Controller Function

Our sign out link will send a GET request to `/auth/sign-out`, so we should prepare to accept that request in the auth controller. We can set up a minimal route with a `res.send` for testing purposes.

In `/controllers/auth.js`:

```javascript
router.get('/sign-out', (req, res) => {
  res.send('The user wants out!')
});
```

Now, we want to get rid of the session attached to the `req` object. Lucky for us, there's a built-in method to the express session object conveniently named `destroy`, allowing us to easily delete the session using `req.session.destroy()`. Nice!

After that, most applications will redirect the user back to the landing page, so let's do just that.

The final controller method should look like this:

```javascript
router.get('/sign-out', (req, res) => {
  req.session.destroy();
  res.redirect('/');
});
```

## Testing the Sign Out Functionality

That's all there is to it! We should be able to manually test the application by signing in, seeing our username on the landing page, and clicking the sign out link to be returned to the landing page as an anonymous guest. You can validate this by seeing whether the sign in and sign up links are present.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-the-user-out-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-the-user-out-complete)
