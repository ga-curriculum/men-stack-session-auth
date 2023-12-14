# ![Mongoose, Express, EJS, and Session Auth - Level Up - Sign In on Sign Up](./assets/hero.png)

## Sign 'Em Up, Sign 'Em In!

A typical user flow for most applications will automatically sign a new user in right after they've created a new account. This makes sense, considering you've just gotten all the same details from the user that they'd need to sign in, and it's kind of annoying to make them go back and fill out a nearly identical form to sign in right after they've signed up.

Let's make our users' lives easier and do this for them in our app.

Briefly, we'll copy the same code from our `sign-in` controller into our `sign-up` controller, and change the redirect statement to go from `/sign-in` straight to the landing page `/` route.

In `controllers/auth.js` right beneath the `User.create` statement in our `sign-up` controller:

```javascript
  req.session.user = {
    username: user.username,
  };

  req.session.save(() => {
    res.redirect('/');
  });
```

If you haven't yet implemented saving sessions to MongoDB, the callback pattern in our `req.session.save()` statement will look unfamiliar. In that case, you can still copy over the same code you're already using in the `sign-in` controller, without the `req.session.save()` callback pattern.

And that's it! You should now see that a newly created user will automatically be redirected to the landing page with a greeting for them, and they're properly signed in to the app.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-in-on-sign-up-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-in-on-sign-up-complete)
