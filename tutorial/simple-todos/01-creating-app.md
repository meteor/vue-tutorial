---
title: "1: Creating the app"
---

## 1.1: Install Meteor
First we need to install Meteor.

Install the latest official Meteor release [following the steps in our docs](https://docs.meteor.com/install.html).

## 1.2: Create Meteor Project

The easiest way to setup Meteor with Vue is by using the command `meteor create` with the option `--vue` and your project name:

```
meteor create --vue simple-todos-vue --prototype
```

Meteor will create all the necessary files for you. 

The files located in the `client` directory are setting up your client side (web), you can see for example `client/main.jsx` where Meteor is rendering your App main component into the HTML.

Also, check the `server` directory where Meteor is setting up the server side (Node.js), you can see the `server/main.js` is initializing your MongoDB database with some data. You don't need to install MongoDB as Meteor provides an embedded version of it ready for you to use.

You can now run your Meteor app using: 

```
meteor run
```

Don't worry, Meteor will keep your app in sync with all your changes from now on.

Your Vue code will be located inside the `imports/ui` directory, and `App.vue` file is the root component of your Vue To-Do app.

Take a quick look in all the files created by Meteor, you don't need to understand them now, but it's good to know where they are.

## 1.3: Install prettier and eslint
Vue do not have native support on vscode for example. To configure Vue on your favorite IDE you'll need to install and configure prettier and eslint.

To install prettier and eslint, run:
```
meteor npm install --save-dev prettier @prettier/plugin-php
```

```
meteor npm install --save-dev eslint eslint-plugin-vue eslint-config-prettier eslint-plugin-prettier
```

Now, create a file called `.eslintrc.js` and paste the code bellow.

`.eslintrc.js`
```javascript
module.exports = {
  root: true,
  env: {
    browser: true,
    node: true,
  },
  extends: ['eslint:recommended', 'plugin:vue/vue3-recommended', 'prettier'],
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': ['error'],
    'vue/require-default-prop': 0,
    'vue/html-indent': ['error', 2],
    'vue/singleline-html-element-content-newline': 0,
    'vue/component-name-in-template-casing': ['error', 'PascalCase'],
  },
  globals: {
    _: true,
  },
}
```

And also create a file called `.prettierrc` and paste the code bellow.

`.prettierrc`
```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "printWidth": 80
}
```


## 1.4: Clean Ui folder

Let's delete some files that we don't need for now. Open your `imports/ui` folder and remove:
- About.vue
- AppMenu.vue
- Hello.vue
- Home.vue
- Info.vue

It means that you'll keep only `App.vue`, `main.js` and `router.js` inside ui folder.

Now that we removed some components, we need to fix our `router.js` file:

`imports/ui/router.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'

export const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: App,
    },
  ],
})
```

At this file we can create some routes and define the component that each route should render.

Also, we need to fix the `App` component, because we're importing a component that does not exist more.

`imports/ui/App.vue`

```javascript
<template>
  <div class="p-8">
    <h1>Hello Meteor</h1>
  </div>
</template>
```

## 1.5: Create Task Component

Create a new file called `Task.vue` in your `ui/components` folder.

This file will export a Vue component called `Task` that will represent one task in your To-Do list. 

`imports/ui/components/Task.vue`
```javascript
<script setup>
import { defineProps } from 'vue'

defineProps(['task'])
</script>

<template>
  <li>{{ task.text }}</li>
</template>

```

As this component will be inside a list you are returning a `li` element.

## 1.6: Create Sample Tasks

As you are not connecting to your server and your database yet let's define some sample data which will be used shortly to render a list of tasks. It will be an array, and you can create a function to return this array.

`imports/ui/App.vue`
```javascript
<script setup>
import Task from './components/Task.vue'

const getTasks = () => {
  return [
    { _id: 1, text: 'This is task 1' },
    { _id: 2, text: 'This is task 2' },
    { _id: 3, text: 'This is task 3' },
  ]
}
</script>
```

You can put anything as your `text` property on each task. Be creative!

## 1.7: Render Sample Tasks

Now we can implement some simple rendering logic with Vue. We can now use our previous `Task` component to render our list items.

See below how we change the template part of the App component to add a 

`imports/ui/App.vue`
```javascript
<template>
  <div class="container">
    <header>
      <h1 class="text-4xl font-bold text-gray-800 my-4">Todo List</h1>
    </header>
    <ul class="list-disc list-inside p-4">
      <Task v-for="task in getTasks()" :key="task._id" :task="task" />
    </ul>
  </div>
</template>
```

> You can read more about Vue iterations [here](https://vuejs.org/api/built-in-directives.html#v-for).

## 1.8 Mobile look

Let's see how your app is looking on Mobile. You can simulate a mobile environment `right clicking` your app in the browser (we are assuming you are using Google Chrome as it is the most popular browser today) and then `inspect`, this will open a new window inside your browser called `Dev Tools`. In the `Dev Tools` you have a small icon showing a Mobile device and a Tablet, see where this icon is:

<img class="step-images" src="/simple-todos/assets/step01-dev-tools-mobile-button.png"/>

Click on it and then select the phone that you want to simulate and in the top bar.

> You can also check your app in your cellphone, you can connect to your App using your local IP in the navigation browser of your mobile browser.
>
> This command should print your local IP for you on Unix systems at least
`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}'`

You are going to see something like this:

<img class="step-images" src="/simple-todos/assets/step01-mobile.png"/>

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step01) 

In the next step we are going to work with MongoDB database to store our tasks.
