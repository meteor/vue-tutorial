---
title: "6: Filter tasks"
---

In this step you will filter your tasks by status and show the quantity of pending tasks.

## 6.1: Filter tasks

First you are going to add a button to show or hide the completed tasks from the list.

We're going to learn how to use Vue's component state to store temporary information that is only used on the client.

First, we need to add a button to our App component:

`imports/ui/App.vue`
```vue
..
<div class="main">
  <TaskForm />
  <div class="filter">
    <button
        v-model="hideCompleted"
        @click="toggleHideCompleted"
    >
      <span v-if="hideCompleted">Show All</span>
      <span v-else>Hide Completed Tasks</span>
    </button>
  </div>
  ..
</div>
..
```

You can see that it reads from `this.hideCompleted`. We'll need to initialize the value of `this.hideCompleted` in the component's data object:

`imports/ui/App.vue`
```vue
..
},
  data() {
    return {
      hideCompleted: false
    };
  },
  methods: {},
..
```

We can update `this.hideCompleted` from an event handler directly, which will then cause the component to re-render:

`imports/ui/App.vue`
```vue
..
  },
  methods: {
    toggleHideCompleted() {
      this.hideCompleted = !this.hideCompleted;
    }
  },
  meteor: {
..
```

## 6.2: Button style

You should add some style to your button, so it does not look gray and without a good contrast. You can use the styles below as a reference:

`client/main.css`
```css
.filter {
  display: flex;
  justify-content: center;
}

.filter > button {
  background-color: #62807e;
}
```

## 6.3 Filter task

Now, we need to update the list of tasks to filter out completed tasks when this.hideCompleted is true:
`imports/ui/App.vue`
```vue
..
meteor: {
  tasks() {
    let filteredTasks = TasksCollection.find({}, { sort: { createdAt: -1 } }).fetch();
    
    if (this.hideCompleted) {
      filteredTasks = filteredTasks.filter(task => !task.checked);
    }

    return filteredTasks;
  }
}
..
```

## 6.4: Meteor Dev Tools Extension

You can install an extension to visualize the data in your Mini Mongo.

[Meteor DevTools Evolved](https://chrome.google.com/webstore/detail/meteor-devtools-evolved/ibniinmoafhgbifjojidlagmggecmpgf) will help you to debug your app as you can see what data is on Mini Mongo. 

<img width="800px" src="/simple-todos/assets/step06-extension.png"/>

You can also see all the messages that Meteor is sending and receiving from the server, this is useful for you to learn more about how Meteor works.

<img width="800px" src="/simple-todos/assets/step06-ddp-messages.png"/>

Install it in your Google Chrome browser using this [link](https://chrome.google.com/webstore/detail/meteor-devtools-evolved/ibniinmoafhgbifjojidlagmggecmpgf).

## 6.5: Pending tasks

Update the App component in order to show the number of pending tasks in the app bar.

You should avoid adding zero to your app bar when there are no pending tasks.

`imports/ui/App.vue`
```vue
..
    <h1>
      📝️ To Do List
      <span v-if="incompleteCount > 0">({{incompleteCount}})</span>
    </h1>
..
    return filteredTasks;
  },
  incompleteCount() {
    return TasksCollection.find({ checked: { $ne: true } }).count();
  }
..
```

You could do both finds in the same `useTracker` and then return an object with both properties but to have a code that is easier to understand code we created two different trackers here.

Your app should look like this:

<img width="200px" src="/simple-todos/assets/step06-all.png"/>
<img width="200px" src="/simple-todos/assets/step06-filtered.png"/>

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step06) 

In the next step we are going to include user access in your app.
