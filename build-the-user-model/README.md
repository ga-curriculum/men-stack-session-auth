# ![Mongoose, Express, EJS, and Session Auth - Build the User Model](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a User model with mongoose.

## Setting up the models directory

Create a directory for our models and the `user.js` file for our User model:

```bash
mkdir models
touch models/user.js
```

## The User Model

Applications vary in their implementation of the user model. Some rely on `email` fields, others simply have `username`. To keep things simple, we'll stick with `username` and `password` for our two fields. For authentication to work properly, we need both fields to be required in the schema.

In `user.js`:

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
});

const User = mongoose.model('User', userSchema);

module.exports = User;
```

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-the-user-model-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-the-user-model-complete)
