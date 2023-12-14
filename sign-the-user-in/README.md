# ![Mongoose, Express, EJS, and Session Auth - Sign the User In](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement sign in functionality in an express application.

## The Sign In Route

Once a user submits their request from the sign in page, we need a route set up to handle this request.

Let's start with the simplest, testable version of this route, so we can immediately confirm that submitting the form will trigger this route:

In `controllers/auth.js`:

```javascript
router.post('/sign-in', async (req, res) => {
  res.send('Request to sign in received!');
});
```

## Confirming a User Exists

First, we need to grab the user from the database, using the username provided in the form. If there is no such user, we have our first failure condition, and can send back an appropriate response to the user. For security reasons, we will recycle the same, vague message for all sign in failures, so that a prospective hacker won't know more than they need to.

Inside the route handler function:

```javascript
const userInDatabase = await User.findOne({ username: req.body.username });
if (!userInDatabase) {
  return res.send('Login failed. Please try again.');
};
```

## bCrypt's Comparison Function

Inside this route, we will again rely on `bcrypt` to determine if their password matches the one stored in the database. The `bcrypt` library has a `compareSync` method that will compare a hashed version of the password with the plain-text version submitted in the form, and return a `true` or `false` response based on whether the passwords match or not. If the result is `false`, we send the same failure message as before.

Beneath the previous validation:

```javascript
const validPassword = bcrypt.compareSync(
  req.body.password,
  userInDatabase.password
);
if (!validPassword) {
  return res.send('Login failed. Please try again.');
}
```

## Session-Based Authentication

If the route handler function has gotten this far, it means we have a successful attempt to sign in to the application. Great! Now what?

There are several competing ways of handling a signed in user for an application, and for this application, we'll use a **session-based** authentication strategy. 

To understand this strategy, we'll first have to talk a bit about **cookies**. Cookies are tiny bits of information stored on a browser, and you're likely familiar by now with the constant messages on websites asking you whether you want to allow or disable cookies on their site. These were once used everywhere by advertisers to track your activity across the internet, and shared across many companies in a vast conspiracy to keep shoving ads in your face. 

However, cookies aren't only used for evil: they are also used to manage shopping carts on e-commerce sites, so if you close your browser tab or navigate away from the page, the site still "remembers" what you'd previously put in your cart.

One downside to using cookies is that they're stored in the browser, giving the user total access to the information stored in them. Therefore, cookies are not supposed to store sensitive information like passwords, since the front-end is considered insecure by nature. 

For our purposes, though, the cookie can store encrypted information about the signed-in user that only our server is able to decrypt. This information is called a **session**.

If you're signed in to an application, you have a session marking you authenticated. A user's future requests to the application will include this session object, automatically stored in their browser as a cookie. Our server will process this object to figure out if a request is coming from a signed in user, and if so, who that user is.

## Setting Up Sessions in Our Server

Sessions are a lot to handle, so we'll need a new library to plug sessions into our express application:

```bash
npm i express-session
```

Since we're encrypting and decrypting the information from this session object, we'll need to create a new environment variable called a `SESSION_SECRET` that is used in the encrypting and decrypting process. We need to keep this string secure, or else anyone would be able to decrypt and read the information stored in the session.

In the `.env` file:
```
SESSION_SECRET=secret-string-unique-to-your-app
```

We want to process the information contained in this session, and which might be useful to any number of routes down the line. Sounds like middleware to me!

In `server.js` at the bottom of our middleware stack, after the `morgan` statement:

```javascript
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true,
  })
);
```

## Creating a Session for a Signed-In User

With that, we're finally ready to return to and complete our sign in route. Once a request has passed the previous validations, we want to create a new session for that user and store their username in that session, so we know future requests are coming from them.

The `session` object has been attached to all incoming requests, so we can access it through the `req` object directly, like so:

```javascript
req.session.user = {
  username: userInDatabase.username,
};
```

Once that's done, we can simply redirect the user back to the home page for now.

```javascript
res.redirect('/');
```

## Full Route Handler

To recap, here's what your full route handler function should look like:

```javascript
router.post('/sign-in', async (req, res) => {
  // First, get the user from the database
  const userInDatabase = await User.findOne({ username: req.body.username });
  if (!userInDatabase) {
    return res.send('Login failed. Please try again.');
  }

  // There is a user! Time to test their password with bcrypt
  const validPassword = bcrypt.compareSync(
    req.body.password,
    userInDatabase.password
  );
  if (!validPassword) {
    return res.send('Login failed. Please try again.');
  }

  // There is a user AND they had the correct password. Time to make a session!
  // Avoid storing the password, even in hashed format, in the session
  // If there is other data you want to save to `req.session.user`, do so here!
  req.session.user = {
    username: userInDatabase.username,
  };

  res.redirect('/');
});
```

## Modify the Landing Page and Index Route

To test this signed-in functionality, let's modify our original landing page, making use of the `req.session` object that every request now has.

Specifically, we'll send a `user` variable to the template that's set equal to the `req.session.user` property we just created in the sign in route. 

If that variable is `undefined`, the template will treat the user as a guest who has not yet signed in to the app.

If there is a value in this variable, however, we can greet the user and not show them the links to sign up or sign in, since they've already done both!

First, let's change the route in `server.js` to the following:

```javascript
app.get('/', (req, res) => {
  res.render('index.ejs', {
    user: req.session.user,
  });
});
```

Then, we can add some logic to the `index.ejs` template, like so:

```html
  <% if (user) { %>
    <h1>Welcome to the app, <%= user.username %>!</h1>
  <% } else { %>
    <h1>Welcome to the app, guest.</h1>
    <p>
      <a href="/auth/sign-up">Sign up</a> or
      <a href="/auth/sign-in">sign in</a>.
    </p>
  <% } %>
```

## Testing Sign In

You should now be able to test all of this functionality through the application directly. Failed sign in attempts should simply result in a message shown in the browser, and require you to navigate back to the sign in page. A successful sign in should result in seeing the landing page again, with a friendly greeting message.

Note, however, that any time you restart the server, the version of the session object being stored in the server's memory will be deleted, and it will be as though you never signed in! If you make any modifications that restart the server, you will have to sign in all over again. 

If you get annoyed enough by this during your development process, we have a solution to store the sessions in MongoDB, instead of the server's local memory, in the level up section of this module.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-the-user-in-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/sign-the-user-in-complete)
