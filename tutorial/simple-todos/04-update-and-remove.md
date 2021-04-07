---
title: '4: Update and Remove'
---

Up until now you have only inserted documents into our collection. Let's take a look at how you can update and remove them by interacting with the user interface.

## 4.1: Add Checkbox

First, you need to add a `checkbox` element to your `Task` component.

> Be sure to add the `readOnly` attribute since we are not using `onChange` to update the state.
>
> You are also invited to experiment and see how the app behaves for learning purposes.

You also want to receive a callback, a function that will be called when the checkbox is clicked.

`imports/ui/components/Task.vue`
```vue
<template>
  <li v-bind:class="taskClassName">
    <input
      type="checkbox"
      readOnly
      v-bind:checked="!!this.task.checked"
    @click="toggleChecked"
  />
  <span class="text">{{ this.task.text }}</span>
</li>
</template>
 ..
```

## 4.2: Toggle Checkbox

Now you can update your task document toggling its `checked` field.

Create a function to change your document and pass it along to your `Task` component.

`imports/ui/components/Task.vue`
```vue
..
<script>
    import { TasksCollection } from "../../api/TasksCollection";
    export default {
      props: ["task"],
      data() {
        return {};
      },
      computed: {
        taskClassName: function() {
          return this.task.checked ? "checked" : "";
        }
      },
      methods: {
        toggleChecked() {
          // Set the checked property to the opposite of its current value
          TasksCollection.update(this.task._id, {
            $set: { checked: !this.task.checked }
          });
        },
      }
    };
</script>
..
```

We call `TasksCollection.update` to check off a task.

The `update` function on a collection takes two arguments. The first is a selector that identifies a subset of the collection, and the second is an update parameter that specifies what should be done to the matched objects.

In this case, the selector is just the `_id` of the relevant task. The update parameter uses `$set` to toggle the `checked` field, which will represent whether the task has been completed.

Your app should look like this:

<img width="200px" src="/simple-todos/assets/step04-checkbox.png"/>

## 4.3: Remove tasks

You can remove tasks with just a few lines of code.

First add a button after text in your `Task` component and receive a callback function.

`imports/ui/components/Task.vue`
```vue
..
<button class="delete" @click="deleteThisTask">
×
</button>
..
```

Now add the removal logic into methods:

`imports/ui/components/Task.vue`
```vue
..  
  methods: {
    ..
    deleteThisTask() {
      TasksCollection.remove(this.task._id);
    }
  }
  ..
```

Your app should look like this:

<img width="200px" src="/simple-todos/assets/step04-delete-button.png"/>

> Review: you can check how your code should be in the end of this step [here](https://github.com/meteor/vue-tutorial/tree/master/src/simple-todos/step04) 

In the next step we are going to improve the look of your app using CSS with Flexbox.
