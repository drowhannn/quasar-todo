<template>
  <q-page class="bg-grey-3 column">
    <div class="row q-pa-sm bg-primary">
      <q-input
        @keyup.enter="addTask"
        outlined
        v-model="newTask"
        placeholder="Add a new todo"
        dense
        bg-color="white"
        class="col"
        square
      >
        <template v-slot:append>
          <q-btn round dense flat icon="add" @click="addTask" />
        </template>
      </q-input>
    </div>
    <q-list class="bg-white" sepearator bordered>
      <q-item
        v-for="(task, index) in tasks"
        :key="task.index"
        v-ripple
        clickable
        @click="task.done = !task.done"
        :class="{ 'done bg-blue-1': task.done }"
      >
        <q-item-section avatar>
          <q-checkbox
            v-model="task.done"
            color="teal"
            class="no-pointer-events"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>
        <q-item-section v-if="task.done" side @click.stop="deleteTask(index)">
          <q-btn flat round dense color="teal" icon="delete" />
        </q-item-section>
      </q-item>
    </q-list>
    <div v-if="!tasks.length" class="no-tasks absolute-center">
      <q-icon name="check" size="100px" color="teal" />
      <div class="text-h5 text-teal text-center">No tasks</div>
    </div>
  </q-page>
</template>

<script setup>
import { reactive, ref } from "vue";
import { useQuasar } from "quasar";

const newTask = ref("");

const $q = useQuasar();

const tasks = reactive(
  localStorage.getItem("todos") ? JSON.parse(localStorage.getItem("todos")) : []
);

const addTask = () => {
  if (newTask.value === "") {
    $q.notify({
      message: "Please enter a task",
      color: "negative",
      position: "top",
      timeout: 500,
    });
  } else {
    tasks.push({
      title: newTask.value,
      done: false,
    });
    localStorage.setItem("todos", JSON.stringify(tasks));
    newTask.value = "";
    $q.notify({
      message: "Task added",
      color: "positive",
      position: "top",
      timeout: 1000,
    });
  }
};

const deleteTask = (index) => {
  $q.dialog({
    title: "Confirm",
    message: "Are you sure you want to delete this task?",
    cancel: true,
    persistent: true,
  }).onOk(() => {
    tasks.splice(index, 1);
    $q.notify({
      message: "Todo deleted",
      position: "top",
      timeout: 1000,
      color: "red",
    });
  });
};
</script>

<style>
.done .q-item__label {
  text-decoration: line-through;
  color: #bbb;
}

.no-tasks {
  opacity: 0.5;
}
</style>
