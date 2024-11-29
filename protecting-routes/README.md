<h1>
  <span class="headline">MEN Stack Session Auth</span>
  <span class="subhead">Protecting Routes</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement controller logic to protect a route using session authentication.

## Protecting routes

In many web applications, you aren't able to see any of the good stuff until you're a logged in user. Most sites also don't want to allow you to create things in their database as an unauthenticated user, so they'll prevent you from ever reaching a form to do so. We can refer to this concept as **protecting routes** from unauthenticated users.

There are many ways to implement this concept, and for our application, we'll stick with the most straightforward approach of using an `if` check to determine if there's a user attached to a given request.

## The VIP lounge

To practice this concept, we'll add a special route just for signed in users to access: the VIP Lounge. We'll present this link to every user on the landing page, but if you're not signed in, the controller function will send you a discouraging message rejecting you from the club. If you **are** signed in, we'll give the user a simple `res.send` message greeting them to the VIP lounge: no need to over-complicate this example.

Let's start by adding an enticing VIP lounge link to the landing page, outside of any conditionals.

In `views/index.ejs`:

```html
<p><a href="/vip-lounge">Get into the VIP Users Only lounge!</a></p>
```

We can now create a route handler for the `/vip-lounge` requests, and since this route won't need to be re-used in future applications, let's just add it to `server.js`.

In `server.js`:

```javascript
app.get("/vip-lounge", (req, res) => {
  if (req.session.user) {
    res.send(`Welcome to the party ${req.session.user.username}.`);
  } else {
    res.send("Sorry, no guests allowed.");
  }
});
```

Note the clean, simple `if` statement that relies on the truthiness of the `req.session.user` value. The `req.session.destroy` method would have eliminated any properties on the `session` object, so if the user has signed out, this property won't exist.

You should now be able to test this route as a signed in user and as an anonymous guest to confirm both responses.

## Real applications of protected routes

Protected routes are common to nearly all web applications, and serve a number of different functions:

1. **Securing personal content:** These routes ensure that private content, such as user profile pages, is only accessible to the owner. It prevents unauthorized access to someone else's personal information.

2. **Controlling resources:** Protected routes are used to restrict the creation, updating, and deletion of resources on the website to users who are authenticated. This means only logged-in users can perform these actions, safeguarding the site from unauthorized changes.

3. **Managing content ownership:** They enable the application to restrict certain actions, like editing or deleting a post, exclusively to the user who created that post. This helps maintain content integrity and ownership rights within the application.

Many functions of protected routes are related to **authorization** in addition to **authentication**, which sound very similar, but have a key difference:

- **Authentication** confirms that you are who you say you are. The app recognizes you.

- **Authorization** confirms that you are allowed to do something specific. The app lets you do something because of who you are.

Simply put, being a signed-in user doesn't give you total power over everything in an application, and we rely on the logic of protected routes to allow users to manage their specific resources, without letting them alter anyone else's.

<!-- [Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/protecting-routes-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/protecting-routes-complete) -->
