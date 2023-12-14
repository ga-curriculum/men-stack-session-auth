# ![Mongoose, Express, EJS, and Session-Auth - Concepts](./assets/tktkhero.png)

**Learning objective:** By the end of this lesson, students will be able to implement session based authentication into their mongoose, express, and ejs application.

## What is Authentication

In short, authentication is the act of determining whether someone is who they say they are.  From a practical standpoint, we will write logic that will allow a person to create a user record (username and password).  Whenever the person wants to do something on our site as they user they created, they need to give us the password to allow them to continue on our site.

## Why use Authentication

As we begin to create more complex databases and associations, we want to make sure that we write logic that ensures a user can create or modify records or things that are only associated with their profile.  The first step to making this happen is to write logic in our apps that allow a user to verify who they say they are.