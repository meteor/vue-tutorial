---
title: '7: Adding User Accounts'
---

## 7.1: Password Authentication

Meteor already comes with a basic authentication and account management system out of the box, so you only need to add the `accounts-password` to enable username and password authentication:

```
meteor add accounts-password
```

> There are many more authentication methods supported. You can read more about the accounts system [here](https://docs.meteor.com/api/accounts.html).

We also recommend you to install `bcrypt` node module, otherwise you are going to see a warning saying that you are using pure-Javascript implementation of it.

```
meteor npm install --save bcrypt
```

> You should always use `meteor npm` instead of only `npm` so you always use the `npm` version pinned by Meteor, this helps you to avoid problems due to different versions of npm installing different modules.

## 7.2: Create User Account

Now you can create a default user for our app, we are going to use `meteorite` as username, we just create a new user on server startup if we didn't find it in the database.

`server/main.js`
```javascript
import { Meteor } from 'meteor/meteor'
import { Accounts } from 'meteor/accounts-base'
import { TasksCollection } from '../imports/api/TasksCollection'

...

const SEED_USERNAME = 'meteorite';
const SEED_PASSWORD = 'password';

Meteor.startup(() => {
  if (!Accounts.findUserByUsername(SEED_USERNAME)) {
    Accounts.createUser({
      username: SEED_USERNAME,
      password: SEED_PASSWORD,
    });
  }

  ...
});
```

You should not see anything different in your app UI yet.

## 7.3: Login Form

You need to provide a way for the users to input the credentials and authenticate, for that we need a form.

Create a new file called `LoginForm.vue` and add a form to it. You should use `Meteor.loginWithPassword(username, password);` to authenticate your user with the provided inputs.

`imports/ui/components/LoginForm.vue`
```javascript
<script setup>
import { Meteor } from 'meteor/meteor'
import { ref } from 'vue'

const username = ref('')
const password = ref('')

const submitForm = (event) => {
    event.preventDefault()
    Meteor.loginWithPassword(username.value, password.value)
}
</script>

<template>
    <form @submit="submitForm " class="flex flex-col justify-center items-center w-full max-w-md mx-auto my-8">
        <div>
            <label for="username" class="block text-gray-700 text-sm font-bold mb-2">Username</label>
            <input type="text" name="username" placeholder="Username" required v-model="username"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
        </div>

        <div class="mt-4">
            <label for="password" class="block text-gray-700 text-sm font-bold mb-2">Password</label>
            <input type="password" name="password" placeholder="Password" required v-model="password"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
        </div>

        <div>
            <button type="submit"
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 mt-4 px-4 rounded focus:outline-none focus:shadow-outline">
                Log in
            </button>
        </div>
    </form>
</template>
```

Ok, now you have a form, let's use it.

## 7.4: Require Authentication

Our app should only allow an authenticated user to access its task management features.

We can accomplish that returning the `LoginForm` component when we don't have an authenticated user, otherwise we return the form, filter, and list component.

We'll need a `ref`, that will be used to know if the user is logged in or not, a `user` variable that will be used to store the user data, and a logic using `watch` to update the `isLogged` ref when the user changes.

`imports/ui/App.vue`
```javascript
...

const hideCompleted = ref(false)
const isLogged = ref(false)

const user = autorun(() => Meteor.userId()).result

watch(
  () => user.value,
  (newUser) => {
    isLogged.value = !!newUser
  },
  { immediate: true }
)

...
```

Then, we can wrap our user functionality in a `<div>` tag and add in the `v-if` directive to conditionally render our user functionality when there is a logged in user:

`imports/ui/App.vue`
```javascript
...

<template>
  <div v-if="isLogged">
    <header class="flex items-center justify-between px-4 py-4 bg-gray-100 border-t border-b border-gray-200">
      <h1 class="text-4xl font-bold text-gray-800 my-4">🚀 To-Do List
        <span v-if="incompleteTasksCount > 0" class="text-sm font-light text-gray-600">({{ incompleteTasksCount }})</span>
      </h1>
    </header>
    <div class="mx-auto max-w-2xl px-4 py-8 sm:px-6 lg:px-8">
      <div class="mb-8 md:w-96 md:mx-auto md:mb-0 md:mt-8 md:px-4 md:py-8 text-center md:bg-gray-100 md:rounded-lg">
        <TaskForm />
        <div>
          <button class="text-sm font-semibold text-gray-600 hover:text-gray-800" @click="toggleHideCompleted">
            <span v-if="hideCompleted">Show all</span>
            <span v-else>Hide completed</span>
          </button>
        </div>
        <ul class="list-none list-inside pr-4 pt-4 md:w-96">
          <Task v-for="task of tasks" :key="task._id" :task="task" />
        </ul>
      </div>
    </div>
  </div>

  <div v-else>
    <LoginForm />
  </div>
</template>
```

## 7.5: Server startup

Every task should have an owner from now on. So go to your database, as you learn before, and remove all the tasks from there:

`db.tasks.remove({});`

Change your `server/main.js` to add the seed tasks using your `meteoriote` user as owner.

Make sure you restart the server after this change so `Meteor.startup` block will run again. This is probably going to happen automatically any way as you are going to make changes in the server side code.

`server/main.js`
```javascript
import { Meteor } from 'meteor/meteor'
import { Accounts } from 'meteor/accounts-base'
import { TasksCollection } from '../imports/api/TasksCollection'

const insertTask = (taskText, user) =>
  TasksCollection.insert({
    text: taskText,
    userId: user._id,
    createdAt: new Date(),
  });

const SEED_USERNAME = 'meteorite';
const SEED_PASSWORD = 'password';

Meteor.startup(() => {
  if (!Accounts.findUserByUsername(SEED_USERNAME)) {
    Accounts.createUser({
      username: SEED_USERNAME,
      password: SEED_PASSWORD,
    });
  }

  const user = Accounts.findUserByUsername(SEED_USERNAME);

  if (TasksCollection.find().count() === 0) {
    [
      'First Task',
      'Second Task',
      'Third Task',
      'Fourth Task',
      'Fifth Task',
      'Sixth Task',
      'Seventh Task',
    ].forEach(taskText => insertTask(taskText, user));
  }
});
```

See that we are using a new field called `userId` with our user `_id` field, we are also setting `createdAt` field.

## 7.7: Task owner

Now you can filter the tasks in the UI by the authenticated user. Use the user `_id` to add the field `userId` to your Mongo selector when getting the tasks from Mini Mongo.

Your ´tasks´ function should look like this:

`imports/ui/App.vue`
```javascript
...

<script setup>
import { Meteor } from 'meteor/meteor'

import { ref, watch } from 'vue'
import Task from './components/Task.vue'
import TaskForm from './components/TaskForm.vue';
import LoginForm from './components/LoginForm.vue';
import { subscribe, autorun } from 'vue-meteor-tracker'
import { TasksCollection } from '../api/TasksCollection'

const hideCompleted = ref(false)
const isLogged = ref(false)

const user = autorun(() => Meteor.userId()).result
const logout = () => Meteor.logout()

watch(
  () => user.value,
  (newUser) => {
    isLogged.value = !!newUser
  },
  { immediate: true }
)

subscribe('tasks')
const tasks = autorun(() => {
  const hideCompletedFilter = { checked: { $ne: true } }
  const userFilter = user.value ? { userId: user.value } : {}

  const pendingOnlyFilter = { ...hideCompletedFilter, ...userFilter }

  return TasksCollection.find(
    hideCompleted ? pendingOnlyFilter : userFilter,
    {
      sort: { createdAt: -1 },
    }
  ).fetch()
}).result

const incompleteTasksCount = autorun(() => {
  return TasksCollection.find({ checked: { $ne: true } }).count()
}).result

const toggleHideCompleted = () => {
  hideCompleted.value = !hideCompleted.value
}

</script>
...
```

Also update the `insert` call to include the field `userId` in the `TaskForm`. You should pass the user from the `App` component to the `TaskForm`.

`imports/ui/components/TaskForm.vue`
```javascript
...

const newTask = ref('')

const user = Meteor.user()

const addTask = () => {
    TasksCollection.insert({
        text: newTask.value.trim(),
        createdAt: new Date(),
        userId: user._id
    })

    newTask.value = ''
}

...
```

## 7.8: Log out

We also can better organize our tasks by showing the username of the owner below our app bar. You can include a new `button` right after our `h1`.

On this you can add an `onClick` handler to logout the user as well. It is very straightforward, just call `Meteor.logout()` on it.

`imports/ui/App.vue`
```javascript
...

const user = autorun(() => Meteor.userId()).result
const logout = () => Meteor.logout()

...

<h1 class="text-4xl font-bold text-gray-800 my-4">🚀 To-Do List
  <span v-if="incompleteTasksCount > 0" class="text-sm font-light text-gray-600">({{ incompleteTasksCount }})</span>
</h1>

<button
  class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
  @click="logout">
      Logout
</button>

...
```

Phew! You have done quite a lot in this step. Authenticated the user, set the user in the tasks and provided a way for the user to log out.

Your app should look like this:

<img class="step-images" src="/simple-todos/assets/new-screenshots/step07/login-page.png"/>
<img class="step-images" src="/simple-todos/assets/new-screenshots/step07/logged-page.png"/>

At this point you probably can try to explore more the app and add a register form by your own. You already know how to menage refs, insert datas, and create components. But feel free to just keep following the tutorial and add the register form later.

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step07) 

In the next step we are going to start using Methods to only change the data after checking some conditions.
