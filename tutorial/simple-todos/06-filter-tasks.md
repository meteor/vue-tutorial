---
title: "6: Filter tasks"
---

In this step you will filter your tasks by status and show the quantity of pending tasks.

## 6.1: Filter tasks

First you are going to add a button to show or hide the completed tasks from the list:

`imports/ui/App.vue`
```javascript
...

<div class="mb-8 md:w-96 md:mx-auto md:mb-0 md:mt-8 md:px-4 md:py-8 text-center md:bg-gray-100 md:rounded-lg">
  <TaskForm />
    <div>
      <button class="text-sm font-semibold text-gray-600 hover:text-gray-800" @click="toggleHideCompleted">
        <span v-if="hideCompleted">Show all</span>
        <span v-else>Hide completed</span>
      </button>
    </div>
    <ul class="list-none list-inside pt-4 md:w-96">
      <Task v-for="task of tasks" :key="task._id" :task="task" />
    </ul>
</div>

...
```

You can see that it reads from `hideCompleted`. We'll need to initialize the value of `hideCompleted` using `ref` in the script section:

`imports/ui/App.vue`
```javascript
...

import { TasksCollection } from '../api/TasksCollection'

const hideCompleted = ref(false)

subscribe('tasks')

...
```

We can update `hideCompleted` from an event handler directly, which will then cause the component to re-render:

`imports/ui/App.vue`
```javascript
...

  const toggleHideCompleted = () => {
  hideCompleted.value = !hideCompleted.value
}
</script>
...
```

## 6.2 Filter task

Now, we need to update the list of tasks to filter out completed tasks when hideCompleted is true:
`imports/ui/App.vue`
```javascript
...

subscribe('tasks')
const tasks = autorun(() => {
  let filteredTasks = TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch()

  if (hideCompleted.value) {
    filteredTasks = filteredTasks.filter(task => !task.checked)
  }

  return filteredTasks
}).result

...
```

## 6.3: Meteor Dev Tools Extension

You can install an extension to visualize the data in your Mini Mongo.

[Meteor DevTools Evolved](https://chrome.google.com/webstore/detail/meteor-devtools-evolved/ibniinmoafhgbifjojidlagmggecmpgf) will help you to debug your app as you can see what data is on Mini Mongo. 

<img class="step-images" src="/simple-todos/assets/new-screenshots/step06/extension-minimongo.png"/>

You can also see all the messages that Meteor is sending and receiving from the server, this is useful for you to learn more about how Meteor works.

<img class="step-images" src="/simple-todos/assets/new-screenshots/step06/extension-ddp.png"/>

Install it in your Google Chrome browser using this [link](https://chrome.google.com/webstore/detail/meteor-devtools-evolved/ibniinmoafhgbifjojidlagmggecmpgf).

## 6.5: Pending tasks

Update the App component in order to show the number of pending tasks in the app bar.

You should avoid adding zero to your app bar when there are no pending tasks.

`imports/ui/App.vue`
```javascript
... 

  return filteredTasks
}).result

const incompleteTasksCount = autorun(() => {
  return TasksCollection.find({ checked: { $ne: true } }).count()
}).result

...

<h1 class="text-4xl font-bold text-gray-800 my-4">🚀 To-Do List
  <span v-if="incompleteTasksCount > 0" class="text-sm font-light text-gray-600">({{ incompleteTasksCount }})</span>
</h1>

...
```

You could do both finds in the same `useTracker` and then return an object with both properties but to have a code that is easier to understand code we created two different trackers here.

Your app should look like this:

<img class="step-images" src="/simple-todos/assets/new-screenshots/step06/showing-all.png"/>
<img class="step-images" src="/simple-todos/assets/new-screenshots/step06/hiding-completed.png"/>

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step06) 

In the next step we are going to include user access in your app.
