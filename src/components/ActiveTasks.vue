<script setup lang="ts">
import { computed } from 'vue';

interface Task {
  id: number;
  name: string;
  isCompleted: boolean;
}

const props = defineProps<{
  tasks: Task[];
}>();

const emit = defineEmits<{
  (e: 'deleteTask', id: number): void;
  (e: 'openEditModal', task: Task): void;
}>();

const activeTasks = computed(() =>
  props.tasks.filter((task) => !task.isCompleted)
);

const deleteTask = (id: number) => emit('deleteTask', id);
const openEditModal = (task: Task) => emit('openEditModal', task);
</script>

<template>
  <v-list class="py-0">
    <v-list-item
      class="px-2"
      v-for="task in activeTasks"
      :key="task.id"
      :height="60"
      :title="task.name"
    >
      <template v-slot:prepend>
        <v-checkbox-btn
          color="green"
          :model-value="task.isCompleted"
          @click="task.isCompleted = !task.isCompleted"
        ></v-checkbox-btn>
      </template>
      <template v-slot:append>
        <v-btn
          variant="text"
          rounded="xl"
          color="grey"
          icon="fa-pen"
          @click="openEditModal(task)"
        ></v-btn>
        <v-btn
          variant="text"
          rounded="xl"
          color="red"
          icon="fa-close"
          @click="deleteTask(task.id)"
        ></v-btn>
      </template>
    </v-list-item>
  </v-list>
</template>
