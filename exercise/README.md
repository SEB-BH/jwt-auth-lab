<h1>
  <span class="headline">JWT Auth Lab</span>
  <span class="subhead">Exercise</span>
</h1>

## Introduction

We will use the auth template provided by Omar to build an app similar to the Session Auth Lab from unit 2 but now it will be a Full Stack MERN stack app using JWT Authentication.


## Backend


# Part 1 - Create the Entry Model

Create a model called **Entry**.

Your model should contain the following fields:

| Field     | Type     | Validation                 |
| --------- | -------- | -------------------------- |
| title     | String   | Required, `maxLength: 100` |
| entryBody | String   | `maxLength: 350`           |
| isPublic  | Boolean  | Default: `false`           |
| owner     | ObjectId | `ref: "User"`              |


# Part 2 - Create route and mount it in server.js

1. Create a `entries.routes.js` file. import the router from express and export it at the bottom of the file. (HINT: The `auth.routes.js` file has the code inside)
2. Inside the `app.js` import the `entries.routes.js` where you see `// Routes Imports` (HINT: It should look almost exactly the same as the other routes imports)
3. Where you see `// Routes go here` add `app.use()` and mount your routes on `/entries`. (HINT: should look almost exactly the same as the `/auth` routes mounting)





# Part 3 - Create an Entry

1. Create the **POST** endpoint on `/entries` that receives the `body` of the new entry.

2. Protect this route using the **verifyToken** middleware.

3. Inside the route, for now just `console.log()` the req.user and notice that the value is the logged in user we saved in the payload of the token:

```javascript
console.log(req.user)
```

Notice that `req.user` contains the **currently logged in user**.

4. Now in the `post` route make sure that when an entry is created the owner is the person who is logged in. REMEMBER from the previous step req.user is the object that contains the logged in user. This also contains the `_id` of the logged in user. So now when the user creates a new entry they should be automatically assigned as the `owner`.

5. Send back a response of the created entry

**CHECKPOINT**: Sign in in Postman, this should return the token back to you. Now send another request to `/entries` with the data for a new entry and add the token in the request header. It should allow you to create an entry if you have the token and if you remove it should return a `401` error


---


# Part 4 - Public Entries

1. Create an endpoint that should return **all entries marked as public**. This route should be at `/entries`. REMEMBER in the model we have a field `isPublic` that is a boolan so when using `.find()` you should ONLY get the entries with `{isPublic: true}`

2. Return all the public entries as an array of objects.

***CHECKPOINT***: Create a new entry with isPublic as false in postman and now send another request to the `/entries` route and see if the entry shows up in the response.

---




# Part 5 - My Entries

Create an endpoint on:

```
/entries/my-entries
```

This endpoint should return **only the entries created by the currently logged in user.**

**Hint:** "You" refers to the user who is currently logged in. **EXTRA HINT**: If you add the `verifyToken` middleware to the route you will have access to `req.user._id` which contains the id of the currently logged in user


### Checkpoint

Verify that:

* You only see entries created by the logged in user. Send a request in postman with the token to check
* Every entry has the logged in user's `_id` saved as the `owner`.



# Part 6 - DELETE and PUT routes

1. Create `delete` route and `put` route. they should let the user delete and edit their own entries.

2. Important part is users should only be able to edit or delete their own entries. So in the beggening of these routes check that the user making the request is the `owner` of the entry. If they are not the owner send back a `403` error

---



# Frontend

Now we will be working in the frontend with the react app. Make sure your backend app is still running

## Part 7 - /my-entries page

1. Create the component page for `/my-entries`, add it to the App.jsx as a route. Make sure to wrap it with `ProtectedRoute.jsx`. this page should make a call to the api to get all my posts (Remember to send the token with the request in the headers)

2. BONUS: Add Loading state, Error state, Empty entries state (`No Entries made yet`)


3. Add it to the navbar Make sure only logged in users can see this in the navbar


## Part 8 - /entries page

1. Create the component page for `/entries` and add it to your `App.jsx`. This route should make an API call to the `/entries` endpoint that returns all public endpoints.

2. BONUS: Add Loading state, Error state, Empty entries state (`No Entries made yet`)

3. Add it to the navbar. This is a public route. users DO NOT need to be logged to see it in the navbar


## Part 9 - /entries/create page

1. Create a page component for creating a new entry. It should contain a form that takes in the data of a new entry and when submitted should send a `POST` request to `/entries`

2. Make sure this route only logged in users can see so Wrap it with `ProtectedRoute.jsx`.

3. **IMPORTANT NOTE** for your `handleChange()` to be able to handle changes to checkbox we have to change it slightly. Use this `handleChange()` from now on:

```jsx
function handleChange(event) {
  const { name, type, value, checked } = event.target;

  setFormData((prev) => ({
    ...prev,
    [name]: type === "checkbox" ? checked : value,
  }));
}
```



# BONUS: Admin Panel

1. Add role for admin in the app in the model for User
2. Now when the token is created add the role to the payload in the /sign-in route
3. Now create a middleware that checks the payload after the token has been verified to check if they are admin
4. Make a route for `/admin`. This route should return all the users in the app
5. Create a `/toggle-admin` route that should only be accessible by admins. This is a POST route where this person should become admin if they are not and should be switched to normal users if they were admin
6. Build the frontend routes for this and make a `isAdmin.jsx` component that would check if the user is admin if not then redirect them to their dashboard
7. Build the page for `/admin` where the admin should be able to go and make other users admin.
8. Add a link to this route in your navbar. Only users who are admin should be able to see it though


## BONUS 2: Soft Delete
Notice that now all admins can delete users. the trust in an admin has to be very strong. now we want it that an admin can "delete" a user but that users data should remain in the database

1. In your user model add a `isDeleted` field that is a boolean. By default it should be false
2. Now when the user logs in we want to check that the user is in the database and not deleted. If the user has the `isDeleted` flag as `true` then we should return a `404` error message
3. Now have it that the /admin route only returns users who have the isDeleted as false
4. Change the functionality for deleting a user in the admin panel POST route to not actually deleting the user but just updating their isDeleted field to true
