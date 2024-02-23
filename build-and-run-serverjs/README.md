# ![MEN Stack Session Auth - Build and Run server.js](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to set up an express server file that uses an environment variable for its port number, logs requests to the console, connects to mongodb, and has the proper middleware stack to process form requests.

## Boilerplate Express Set-Up

Let's begin with the same boilerplate express code and libraries we've come to know and love.

Install packages:

```bash
npm i express mongoose dotenv ejs morgan
```

Create your `.env` file at the root level of the repository and include the `MONGODB_URI` connection string variable.

```bash
touch .env
```

Your `.env` should look like this:

```
MONGODB_URI=<connection-string-from-mongodb-atlas>
```

Include the following boilerplate code in `server.js`:

```javascript
const dotenv = require("dotenv");
dotenv.config();
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const methodOverride = require("method-override");
const morgan = require("morgan");

const port = process.env.PORT ? process.env.PORT : "3000";

mongoose.connect(process.env.MONGODB_URI);

mongoose.connection.on("connected", () => {
  console.log(`Connected to MongoDB ${mongoose.connection.name}.`);
});

app.use(express.urlencoded({ extended: false }));
app.use(methodOverride("_method"));
// app.use(morgan('dev'));

app.listen(port, () => {
  console.log(`The express app is ready on port ${port}!`);
});
```

## process.env.what???

You probably noticed this new `port` variable we're defining and using in the `app.listen` statement. This will come in handy when we're deploying our applications and cannot know in advance which port the hosting service will use.

The syntax commonly seen for this variable is a **ternary** statement, which is essentially a condensed `if...else` statement. You could replace it with the following:

```javascript
let port;
if (process.env.PORT) {
  port = process.env.PORT;
} else {
  port = 3000;
}
```

The ternary statement decides which value to assign to the variable. If the `process.env.PORT` value turns out truthy, it uses that. Otherwise, if it's `undefined`, and therefore falsey, it uses the hardcoded `3000` value we've been running locally all along. This way, we get to use the environment variable in a hosted environment, and still use `3000` when running the application locally.

## Run the server.js

Let's confirm the application is working before moving on. Run `server.js` and confirm you've received the mongoose connection message.

```bash
nodemon
```

You should see something like:

```
Connected to MongoDB <mongoose-uri>.
The express app is ready on port 3000!
```

[Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-and-run-serverjs-start)

[Complete Code](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template/tree/build-and-run-serverjs-complete)
