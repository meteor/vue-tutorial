---
title: "3: Forms and Events"
---

All apps need to allow the user to perform some types of interaction with the data that is stored. In our case, the first type of interaction is to insert new tasks, or our app would not have much value, would it?

One of the main ways in which a user can insert or edit data in a website is through forms, in most cases it is a good idea to use the `<form>` tag since it gives semantic meaning to the elements inside it.

## 3.1: Create Task Form

First we need to create a simple form component to encapsulate our logic.

Create a new file `TaskForm.vue` in your `ui` folder.

`imports/ui/components/TaskForm.vue`
```javascript
<script setup>
import { ref } from 'vue'
import { TasksCollection } from '../../api/TasksCollection';

const newTask = ref('')

const addTask = () => {
  console.log(newTask.value)
}
</script>

<template>
    <form @submit.prevent="addTask">
        <input
            class=" border border-gray-300 rounded-md py-2 px-4 mr-2 text-gray-600 text-sm focus:outline-none focus:border-gray-400 focus:ring-0"
            type="text" v-model="newTask" placeholder="Type to add new tasks" />
        <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded" type="submit">Add Task</button>
    </form>
</template>
```

This form will have an input element added to it that has a `v-model` attribute. The `newTask` data field will now be bound via two-way binding to the input element's value.

You can also see that the form element has a `@submit.prevent` attribute that will call the `addTask` method when the form is submitted. The `@` is a shorthand for `v-on:`. The `prevent` modifier will prevent the default behavior of the form, which is to reload the page.

## 3.2: Update the App component

Then we can simply add this to our `App` component above your list of tasks:

`imports/ui/App.vue`
```javascript
<script setup>
import Task from './components/Task.vue'
import TaskForm from './components/TaskForm.vue';
import { subscribe, autorun } from 'vue-meteor-tracker'
import { TasksCollection } from '../api/TasksCollection'

subscribe('tasks')
const tasks = autorun(() => TasksCollection.find({}).fetch()).result
</script>

<template>
  <div class="container">
    <header>
      <h1 class="text-4xl font-bold text-gray-800 my-4">Todo List</h1>
    </header>
    <TaskForm />
    <ul class="list-disc list-inside p-4">
      <Task v-for="task of tasks" :key="task._id" :task="task" />
    </ul>
  </div>
</template> 
```

## 3.3: Add Insert Operation

Now you can edit the `addTask` function to insert a new task into the database.

`imports/ui/components/TaskForm.vue`
```javascript
...

const addTask = () => {
    TasksCollection.insert({
        text: newTask.value.trim(),
        createdAt: new Date()
    })

    newTask.value = ''
}

...
```

Also insert a date `createdAt` in your `task` document, so you know when each task was created.

Inside the event, we are adding a task to the `tasks` collection by calling `TasksCollection.insert()`. We can assign any properties to the task object, such as the time created, since we don't ever have to define a schema for the collection.

Now if we try to add anything to the collection we will get denied. For our prototyping purposes we'll add the insecure package to get around this for the moment:
```bash
meteor add insecure
```

Being able to insert anything into the database from the client isn't very secure, but it's okay for now. In step 8 we'll learn how we can make our app secure and restrict how data is inserted into the database.

## 3.5: Show Newest Tasks First

Now you just need to make a change which will make users happy: we need to show the newest tasks first. We can accomplish quite quickly by sorting our [Mongo](https://guide.meteor.com/collections.html#mongo-collections) query.

`imports/ui/App.vue`
```javascript
...

const tasks = autorun(() => TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch()).result

...
```

Your app should look like this:

<img class="step-images" src="/simple-todos/assets/new-screenshots/step03/newest-task.png"/>

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step03) 

In the next step we are going to update your tasks state and provide a way for users to remove tasks.
