<script setup lang="ts">
import { ref, computed } from 'vue';
import AllTasks from '@/components/AllTasks.vue';
import ActiveTasks from '@/components/ActiveTasks.vue';
import CompletedTasks from '@/components/CompletedTasks.vue';

interface Task {
  id: number;
  name: string;
  isCompleted: boolean;
}

const task = ref('');
const taskEdit = ref();

const tasks = ref<Task[]>([]);
const uncompletedTasks = ref(0);
const completedTasks = ref(0);
const totalTasks = ref(0);
const activeCategory = ref(1);
const categoriesTasks = ref([
  {
    id: 1,
    name: 'Todas',
  },
  {
    id: 2,
    name: 'Activas',
  },
  {
    id: 3,
    name: 'Completadas',
  },
]);
const snackbar = ref(false);
const message = ref('');
const modal = ref(false);

const addTask = () => {
  if (task.value.length === 0) {
    message.value = 'Debes ingresar una tarea.';
    snackbar.value = true;
    return;
  }

  tasks.value.push({
    id: Date.now(),
    name: task.value,
    isCompleted: false,
  });

  task.value = '';
};

const deleteTask = (task_id: number) => {
  tasks.value = tasks.value.filter((task) => task_id !== task.id);
};
const updateTask = (newTask: any) => {
  if (newTask.name === '') {
    message.value = 'Debes ingresar una tarea.';
    snackbar.value = true;
    return;
  }

  tasks.value.forEach((task, index) => {
    if (task.id === newTask.id) {
      tasks.value[index] = { ...task, ...newTask };
      modal.value = false;
      return;
    }
  });
};

const openEditModal = (task: any) => {
  taskEdit.value = { ...task };
  modal.value = true;
};

const getUncompletedTasks = computed(() => {
  if (tasks.value.length === 0) return 'No hay tareas disponibles.';

  uncompletedTasks.value = tasks.value.filter(
    (task) => !task.isCompleted
  ).length;

  return uncompletedTasks.value === 1
    ? 'Queda 1 tarea.'
    : `Quedan ${uncompletedTasks.value} tareas.`;
});

const getCompletedTasks = computed(() => {
  if (tasks.value.length === 0) return 0;

  totalTasks.value = tasks.value.length;
  completedTasks.value = tasks.value.filter((task) => task.isCompleted).length;

  return (completedTasks.value / totalTasks.value) * 100;
});

const clearCompletedTasks = () => {
  tasks.value = tasks.value.filter((task) => !task.isCompleted);
};

const disabledClearButton = computed(() => {
  return completedTasks.value === 0;
});
</script>

<template>
  <v-app>
    <div class="container">
      <v-col>
        <h1 class="text-center text-uppercase text-h2 text-cyan">Todo List</h1>
        <v-text-field
          class="mt-10"
          label="Qué quieres hacer?"
          variant="solo"
          single-line
          hide-details
          append-inner-icon="fa-plus"
          @click:append-inner="addTask"
          @keyup.enter="addTask"
          v-model="task"
        ></v-text-field>
        <v-card class="mt-5">
          <v-progress-linear
            :model-value="getCompletedTasks"
            color="cyan"
            height="8"
          ></v-progress-linear>
          <v-card-actions class="d-block d-sm-flex justify-space-between">
            <h4 class="text-body-2 mt-3 mt-sm-0">{{ getUncompletedTasks }}</h4>
            <div class="mt-4 mt-sm-0">
              <v-btn
                v-for="category in categoriesTasks"
                :key="category.id"
                color="cyan"
                :variant="activeCategory === category.id ? 'tonal' : 'text'"
                @click="activeCategory = category.id"
                >{{ category.name }}</v-btn
              >
            </div>
          </v-card-actions>
          <v-divider></v-divider>
          <v-card-text class="pa-0 card-text overflow-auto">
            <AllTasks
              v-show="activeCategory === 1"
              :tasks="tasks"
              @delete-task="deleteTask"
              @open-edit-modal="openEditModal"
            />
            <ActiveTasks
              v-show="activeCategory === 2"
              :tasks="tasks"
              @delete-task="deleteTask"
            />
            <CompletedTasks
              v-show="activeCategory === 3"
              :tasks="tasks"
              @delete-task="deleteTask"
            />
          </v-card-text>
        </v-card>
        <v-btn
          class="mt-5 text-white"
          :disabled="disabledClearButton"
          variant="flat"
          color="cyan"
          rounded="xl"
          block
          @click="clearCompletedTasks"
          size="large"
          >Limpiar Completados</v-btn
        >
      </v-col>
    </div>
    <v-snackbar v-model="snackbar" location="top right">
      {{ message }}

      <template v-slot:actions>
        <v-btn variant="text" @click="snackbar = false">
          <v-icon icon="fa-close"></v-icon>
        </v-btn>
      </template>
    </v-snackbar>
    <div v-if="modal" class="modal px-3">
      <v-card class="bg-transparent card-modal text-center" flat>
        <v-text-field
          v-model="taskEdit.name"
          hide-details
          variant="solo"
          @keyup.enter="updateTask(taskEdit)"
          append-inner-icon="fa-edit"
          @click:append-inner="updateTask(taskEdit)"
        ></v-text-field>
        <h1 class="text-body-2 text-white mt-4">
          Presiona ENTER para actualizar
        </h1>
        <v-btn
          class="mt-14"
          flat
          icon="fa-close"
          variant="text"
          color="white"
          @click="modal = false"
        >
        </v-btn>
      </v-card>
    </div>
  </v-app>
</template>

<style>
.container {
  display: flex;
  align-items: center;
  margin: 0 auto;
  width: 50%;
  height: 100vh;
}
.modal {
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
}
.card-modal {
  width: 50%;
}
.card-text {
  height: 300px;
}

@media (max-width: 768px) {
  .container,
  .card-modal {
    width: 100%;
  }
}

@media (min-width: 769px) and (max-width: 1024px) {
  .container .card-modal {
    width: 70%;
  }
}

@media (min-width: 1025px) and (max-width: 1440px) {
  .container .card-modal {
    width: 60%;
  }
}
</style>
