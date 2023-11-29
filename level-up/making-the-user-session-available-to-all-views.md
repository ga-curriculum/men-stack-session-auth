# ![Mongoose, Express, EJS, and Session Auth - Level Up - Making the User Session Available to All Views](./assets/hero.png)

tktk Notes: For simplicity, make the **Protecting Routes with Middleware** Level Up a prereq for this Level Up. Demonstrate adding a `user: req.session.user` property to the `res.locals` object in custom middleware. `touch middleware/pass-user-session-to-view.js`.

tktk Notes: The **Protecting Routes with Middleware** level should do a lot of the heavy lifting around what custom middleware is, what `next()` is doing, etc. so don't feel a huge need to dive into that here. Add this middleware to the middleware chain in `server.js`. Emphasize how this middleware prevents us from having to ever pass `user` to a view! Demo this by removing the locals object from the `/` route.

[tktk Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-start)

[tktk Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/making-user-available-to-all-views-complete)
