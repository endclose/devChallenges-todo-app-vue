<script setup>
import { ref, computed, onMounted, onUpdated } from "vue";

const tasks = ref([]);
const task = ref("");
const currentMenu = ref(1);

const addTask = () => {
  if (!task.value) {
    return;
  }
  tasks.value.push({
    id: tasks.value.length + 1,
    text: task.value,
    done: false,
  });
  task.value = "";
};
const deleteTask = (taskid) => {
  tasks.value = tasks.value.filter((task) => task.id !== taskid);
};
const setCurrentMenu = (menuNumber) => {
  currentMenu.value = menuNumber;
};
const deleteAllCompleted = () => {
  tasks.value = tasks.value.filter((task) => task.done !== true);
};

const isThereDoneTask = computed(() => {
  const taskArrayFiltered = tasks.value.filter((task) => task.done === true);
  return !!taskArrayFiltered.length;
});

onMounted(() => {
  const localStorageTasks = JSON.parse(localStorage.getItem("tasks"));
  if (!localStorageTasks) return;
  tasks.value = [...localStorageTasks];
});
onUpdated(() => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
});
</script>
<template>
  <main>
    <div class="container">
      <h1 class="center">#todo</h1>
      <nav>
        <ul class="nav-menu">
          <li
            :class="
              currentMenu === 1 ? 'nav-menu-item active' : 'nav-menu-item'
            "
            @click="setCurrentMenu(1)"
          >
            All
            <span></span>
          </li>
          <li
            :class="
              currentMenu === 2 ? 'nav-menu-item active' : 'nav-menu-item'
            "
            @click="setCurrentMenu(2)"
          >
            Active
            <span></span>
          </li>
          <li
            :class="
              currentMenu === 3 ? 'nav-menu-item active' : 'nav-menu-item'
            "
            @click="setCurrentMenu(3)"
          >
            Completed
            <span></span>
          </li>
        </ul>
      </nav>
      <form
        id="input-task-form"
        @submit.prevent="addTask"
        v-if="currentMenu !== 3"
      >
        <input
          id="task-input"
          type="text"
          placeholder="add details"
          v-model="task"
        />
        <button :disabled="!task" class="primary">Add</button>
      </form>
      <div class="tasks">
        <ul>
          <template v-for="task in tasks">
            <li class="task-item" v-if="currentMenu === 1">
              <div class="text-task">
                <input type="checkbox" v-model="task.done" />
                <span :class="task.done ? 'task-done' : ''">
                  {{ task.text }}</span
                >
              </div>
            </li>
            <li class="task-item" v-if="currentMenu === 2 && !task.done">
              <div class="text-task">
                <input type="checkbox" v-model="task.done" />
                <span :class="task.done ? 'task-done' : ''">
                  {{ task.text }}</span
                >
              </div>
            </li>
            <li class="task-item" v-if="currentMenu === 3 && task.done">
              <div class="text-task">
                <input type="checkbox" v-model="task.done" />
                <span :class="task.done ? 'task-done' : ''">
                  {{ task.text }}</span
                >
              </div>
              <span
                style="float: right"
                class="material-icons-outlined delete-button"
                @click="deleteTask(task.id)"
                >delete</span
              >
            </li>
          </template>
        </ul>
      </div>
      <div v-if="!isThereDoneTask && currentMenu === 3" class="center">
        There's no completed tasks
      </div>
      <button
        v-if="currentMenu === 3 && isThereDoneTask"
        class="red-button"
        style="float: right"
        @click="deleteAllCompleted"
      >
        <span class="material-icons-outlined">delete</span>
        delete all
      </button>
    </div>
  </main>
  <footer>
    created by  <a href="">endclose</a>  - devChallenges.io
  </footer>
</template>
