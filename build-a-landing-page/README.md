# ![MEN Stack Session Auth - Build a Landing Page](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to build an Express route that renders a landing page for an application.

## The Landing Page

For our authentication app, we'll use a landing page to direct users to links where they can sign up or sign in to the authenticated sections of the application.

## Build the route

We'll need to start with a route in our `server.js` file. We'll set it up to send a simple response for testing, then come back and link up our EJS file later. Remember, this code, like all express routes, should be placed above the `app.listen()` method.

```javascript
// server.js

// GET /
app.get("/", async (req, res) => {
  res.send("hello, friend!");
});
```

Test the route at this point by browsing to `localhost:3000`. You should receive a message in the browser that says hello, friend!

We've confirmed the route is valid at this point, let's do more than just send a message back.

## Create a `views` directory and the landing page

We're going to need a `views` directory to place all of our templates in, so let's create it now.

```bash
mkdir views
```

Our landing page will be an `index.ejs` file that is inside of this directory. Let's create that too:

```bash
touch views/index.ejs
```

Open that file, and add HTML boilerplate, along with some content inside of the `<body>` tag. Don't forget to also change the title!

```html
<!-- views/index.ejs -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Home Page</title>
  </head>
  <body>
    <h1>Welcome to the app!</h1>
  </body>
</html>
```

With that built, all we need to do is send it in response to a request on the `/` route of our application. Change the existing `res.send()` method to a `res.render()` of this file we just created:

```javascript
// server.js

// GET /
app.get("/", async (req, res) => {
  res.render("index.ejs");
});
```

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-a-landing-page-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-a-landing-page-complete)
