# ![Mongoose, Express, EJS, and Session Auth - Recap](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to articulate the process of authentication in an express application.

## Recap

Congratulations! We've completed an express application in which users can sign up with a new account, sign in to the app, view resources restricted to signed-in users, and sign themselves out. This was a big project, so it's worth reviewing the key aspects from each of the major steps we took.

## Modularization of the Controllers

We have some great news for you: once you've written authentication once, you can re-use it for all your future express applications. This is why we separated our routes into a new `controllers` directory that you can easily copy into future projects. While we didn't create a second model in this application, you can imagine that future applications will start with a User as the very first model, then add more models for the resources users are allowed to create and manage in the app. Nearly all of the app ideas swimming around in your head are apps that incorporate users, authentication, and all the concepts that go with them, so it's a great template to kickstart your next express application.

## Session-Based Authentication

For this application, we chose session-based authentication as our strategy. This relies on cookies, stored in both the browser and the server's internal memory, and encrypted using a `SESSION_SECRET` environment variable. This cookie gets attached to all future requests coming from users of our site, and our server will check to confirm that the user hasn't added anything to the cookie that doesn't match the version stored in the server.

## Encrypted Passwords

One very, very important thing we did was to encrypt the user's passwords before storing them in the database. If you remember only one thing from this exprience, make it this: **never store passwords in plain-text**.

We relied on the industry standard `bcrypt` library to do the hard work of cryptographic encrypting and decryption, and this should carry a lesson for all security-related programming: if possible, find an industry standard tool that has been tested and relied on by the major players. This is not the time to roll-your-own cryptography function!

## User Model Constraints

We used a simple `if` check to determine whether a username was already taken, and this should work for our demo applications. For production-ready applications, however, you'd want a bit more validation than that. For example, in our application, `spongebob` and `sPoNgEbOb` would be two totally separate usernames thanks to the difference in capitalization. Not great!

Most applications will keep the actual `username` field in all lowercase in the database, and create a separate `display name` field allowing users to be free to change capitalization and text to their hearts content. Many social media tags work this way: you can change your display name daily if you like, but the actual username handle remains static.

There's also the concept of using emails to validate accounts before permitting them to do certain things in the app. This `email` field would also have to be unique to each user model, and it could be used to send a special link through email to new users requiring them to validate before continuing in the app. We would also rely on this email when implementing functionality for resetting a forgotten password, which brings with it even further programming challenges.

## We Have to End Somewhere!

For now, username validations, email verification, and resetting passwords are all well beyond the scope of our introduction to authentication, but we want to give you an idea of the many authentication-related tasks that a mature, production-ready application needs to consider. You have enough in our authentication template here to create a great portfolio-worthy demo app, but not quite enough to start signing up anonymous users who expect your app to work like all the other great web apps out there.

Fortunately, since these needs are mostly the same for all applications, there are plenty of ready-made libraries and solutions out there competing for developer adoption. For express in particular, `passport.js` is a common authentication solution that's relatively easy to plug in, and allows you to add social authentication such as signing in with Google accounts.

There are also plenty of minor optimizations in the Level Up section of this module, including saving sessions to your mongo database, creating a smooth user experience of signing in automatically after signing up, and more!