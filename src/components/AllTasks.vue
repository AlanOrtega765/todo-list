<script setup lang="ts">
interface Task {
  id: number;
  name: string;
  isCompleted: boolean;
}

defineProps<{
  tasks: Task[];
}>();

const emit = defineEmits<{
  (e: 'deleteTask', id: number): void;
  (e: 'openEditModal', task: Task): void;
}>();

const deleteTask = (id: number) => emit('deleteTask', id);
const openEditModal = (task: Task) => emit('openEditModal', task);
</script>

<template>
  <v-list class="py-0">
    <v-list-item
      class="px-2"
      v-for="task in tasks"
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
