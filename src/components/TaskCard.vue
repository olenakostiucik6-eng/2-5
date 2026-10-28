<script setup>
import { computed } from 'vue';
import StatusBadge from './StatusBadge.vue';
import PriorityBadge from './PriorityBadge.vue';

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits([
  'status-change',
  'priority-change',
  'task-select',
]);

const statusSequence = ['todo', 'progress', 'done'];
const prioritySequence = ['low', 'medium', 'high'];

const statusActionText = computed(() => {
  const actions = {
    todo: 'Взяти в роботу',
    progress: 'Завершити',
    done: 'Повернути в план',
  };

  return actions[props.task.status] || 'Змінити статус';
});

const formattedDate = computed(() => {
  if (!props.task.dueDate) return 'Без терміну';

  const date = new Date(`${props.task.dueDate}T12:00:00`);

  if (Number.isNaN(date.getTime())) {
    return 'Дата не визначена';
  }

  return new Intl.DateTimeFormat('uk-UA', {
    day: '2-digit',
    month: 'long',
    year: 'numeric',
  }).format(date);
});

function changeStatus() {
  const index = statusSequence.indexOf(props.task.status);
  const nextStatus =
    statusSequence[(index + 1) % statusSequence.length];

  emit('status-change', {
    id: props.task.id,
    status: nextStatus,
  });
}

function changePriority() {
  const index = prioritySequence.indexOf(props.task.priority);
  const nextPriority =
    prioritySequence[(index + 1) % prioritySequence.length];

  emit('priority-change', {
    id: props.task.id,
    priority: nextPriority,
  });
}

function selectTask() {
  emit('task-select', props.task);
}
</script>

<template>
  <f7-card class="tf3-task-card">
    <f7-card-content>
      <div class="tf3-card-heading">
        <button
          type="button"
          class="tf3-task-title-select"
          @click="selectTask"
        >
          {{ task.title }}
        </button>

        <StatusBadge :status="task.status" />
      </div>

      <p class="tf3-card-description">
        {{ task.description }}
      </p>

      <div class="tf3-card-meta">
        <div class="tf3-meta-item">
          <span class="tf3-meta-label">Пріоритет:</span>
          <PriorityBadge :priority="task.priority" />
        </div>

        <div class="tf3-meta-date">
          Термін: {{ formattedDate }}
        </div>
      </div>

      <div class="tf3-card-actions">
        <button
          type="button"
          class="tf3-action tf3-action--primary"
          @click="changeStatus"
        >
          {{ statusActionText }}
        </button>

        <button
          type="button"
          class="tf3-action"
          @click="changePriority"
        >
          Змінити пріоритет
        </button>
      </div>
    </f7-card-content>
  </f7-card>
</template>