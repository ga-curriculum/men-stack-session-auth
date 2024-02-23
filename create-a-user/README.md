# ![MEN Stack Session Auth - Create a User](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create new user accounts through the sign up page in an express application.

## Get To The Action

For our form to make useful submissions to our server, we need to modify the `action` and `method` properties so the form sends a `POST` request to `/auth/sign-up` route.

Modify the following in `views/auth/sign-up.ejs`:

```html
<form action="/auth/sign-up" method="POST"></form>
```

## Processing the Request

Now, we need to create the controller function handling this request. Let's start with a simple `res.send` response so we can test as we go. Note the use of `async`, as this function will eventually require a database call.

In `controllers/auth.ejs`:

```javascript
router.post("/sign-up", async (req, res) => {
  res.send("Form submission accepted!");
});
```

Since we want this route to create a new User in the database, we'll first need to import the model into this file:

```javascript
const User = require("../models/user.js");
```

It would be **great** if we could simply do a `User.create` statement in the route and move on with our lives. Unfortunately, authentication is a bit more complicated than that! There are several problems we need to deal with:

1. Usernames need to be unique: two people can't share the same username!
1. Ensure that the password and confirmPassword fields match
1. Passwords cannot be stored directly in plain-text in the database

## Enforcing Unique Usernames

To make sure somebody hasn't already taken the username being submitted, we need to check the database for any existing user with that username.

Inside the route handler function:

```javascript
const userInDatabase = await User.findOne({ username: req.body.username });
if (userInDatabase) {
  return res.send("Username already taken.");
}
```

## Checking Password and ConfirmPassword

This check will be a little simpler, as we only need a simple comparison of values already submitted through the form:

Inside the route handler function, below the previous validation:

```javascript
if (req.body.password !== req.body.confirmPassword) {
  return res.send("Password and Confirm Password must match");
}
```

## Securely Storing Passwords

If the previous validations have passed, we're ready to create the user in the database. However, we can't store passwords in plain-text, for security reasons. If the database were ever compromised, the hacker would have access to all our user's passwords, which are probably recycled in other applications: a security nightmare!

For this and most security-related problems, we should not attempt to solve this problem ourselves. Instead, we rely on industry-standard tools that can ensure a securely encrypted password gets stored in our database.

Using the `bcrypt` library, we will perform what's called a `hashing` operation which will scramble the user's password into a difficult-to-decrypt string. The hashing function also requires the use of a `salt` string, which ensures that even if two users have the exact same passwords, we end up with different encrypted strings in the database.

First, install the bcrypt package and import it at the top of the file:

```bash
npm i bcrypt
```

```javascript
const bcrypt = require("bcrypt");
```

Next, add the following lines to our controller function, beneath our previously written validations:

```javascript
const hashedPassword = bcrypt.hashSync(req.body.password, 10);
req.body.password = hashedPassword;

const user = await User.create(req.body);

res.send(`Thanks for signing up ${user.username}`);
```

The number `10` in the `hashSync` method represents the amount of salting we want the hashing function to execute: the higher the number, the harder it will be to decrypt the password. However, higher numbers will take longer for our application when we're checking a user's password, so we need to keep it reasonable for performance reasons.

## Test the Route

You should now be able to navigate to `localhost:3000/auth/sign-up` and create a new user account through this form. You can validate that the password was properly encrypted by viewing the user in MongoDB Atlas.

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/create-a-user-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/create-a-user-complete)
