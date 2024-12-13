<script setup>
  import { computed, onMounted, ref, watch } from 'vue';

  const showCompletedTasks = ref(false);
  const showForm = ref(false);
  const tasks = ref([]);
  const taskName = ref('');

  const filterCompletedTasks = computed(() => {
    return showCompletedTasks.value
      ? tasks.value.filter((task) => task.completed)
      : tasks.value;
  });

  const addTask = () => {
    if (taskName.value.length < 3) {
      alert('Task name must be at least 3 characters long.');
    } else {
      tasks.value.push({
        id: Date.now(),
        title: taskName.value,
        completed: false,
      });
      taskName.value = '';
    }
  };

  watch(
    tasks,
    (newVal) => {
      localStorage.setItem('tasks', JSON.stringify(newVal));
    },
    { deep: true }
  );

  onMounted(() => {
    tasks.value = JSON.parse(localStorage.getItem('tasks')) || [];
  });

  const removeTask = (id) => {
    tasks.value = tasks.value.filter((task) => task.id !== id);
  };

  const hasCompletedTasks = computed(() => {
    return tasks.value.some((task) => task.completed);
  });
</script>

<template>
  <div class="w-[500px]">
    <h2 class="text-3xl mb-4">Task Management App</h2>
    <button @click="showForm = !showForm">
      {{ showForm ? 'Hide' : 'Show' }} Task Form
    </button>

    <form v-if="showForm" @submit.prevent="addTask" class="my-4">
      <input
        v-model="taskName"
        type="text"
        placeholder="Enter task title"
        class="w-full border border-gray-300 px-4 py-2 rounded-md"
      />
      <button type="submit" class="bg-slate-700 text-white mt-3 w-1/2">
        Add Task
      </button>
    </form>

    <div>
      <div v-if="filterCompletedTasks.length === 0">No Tasks Available</div>
      <div v-else>
        <h3 class="mt-4">Tasks:</h3>

        <button
          v-if="hasCompletedTasks"
          @click="showCompletedTasks = !showCompletedTasks"
          class="mt-3 text-xs uppercase"
        >
          {{ showCompletedTasks ? 'Show All' : 'Show Completed' }}
        </button>

        <div
          v-for="task in filterCompletedTasks"
          :key="task.id"
          :class="{
            'bg-green-500': task.completed,
            'border-transparent': task.completed,
          }"
          class="flex items-center gap-1 border border-gray-300 p-4 rounded-md my-4"
        >
          <div class="flex-1 text-start">{{ task.title }}</div>
          <button
            @click="task.completed = !task.completed"
            class="bg-green-600 text-white text-sm uppercase"
          >
            {{ task.completed ? 'Undo' : ' Complete' }}
          </button>
          <button
            @click="removeTask(task.id)"
            class="bg-red-600 text-white text-sm uppercase"
          >
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .read-the-docs {
    color: #888;
  }
</style>
