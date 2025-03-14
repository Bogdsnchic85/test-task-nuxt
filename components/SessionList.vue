<template>
  <div class="sessions">
    <h2 class="sessions-title">Активные сессии</h2>
    <ul class="sessions-list">
      <li
        v-for="session in sessions"
        :key="session.session_id"
        class="session-item"
      >
        <div class="session-info">
          <p class="session-id">Сессия: <code>{{ session.session_id }}</code></p>
          <p class="session-device">Браузер: {{ session.name }}</p>
          <p class="session-date">Последнее обновление: {{ formatDate(session.last_update) }}</p>
        </div>
        <button
          v-if="!session.is_current"
          @click="deleteSession(session.session_id)"
          class="btn btn-delete"
        >
          Удалить
        </button>
        <span v-else class="current-session">Текущая сессия</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { formatDate } from '~/utils/dateFormatter'

const props = defineProps({
  sessions: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['delete-session'])

const deleteSession = (sessionId) => {
  emit('delete-session', sessionId)
}
</script>

<style scoped lang="scss">
@import '~/assets/styles/variables';

.sessions {
  margin-top: $spacing-large;
}

.sessions-title {
  font-size: 22px;
  font-weight: 600;
  color: $text-color;
  margin-bottom: 15px;
}

.sessions-list {
  list-style: none;
  padding: 0;
}

.session-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid #eaecef;
  transition: background-color 0.3s ease;

  &:last-child {
    border-bottom: none;
  }

  &:hover {
    background-color: #f8f9fa;
  }
}

.session-info {
  flex: 1;
}

.session-id {
  font-size: 16px;
  color: $text-color;
  margin: 0;
}

.session-device,
.session-date {
  font-size: 14px;
  color: $muted-text-color;
  margin: 5px 0 0;
}

.current-session {
  font-size: 14px;
  color: #27ae60;
  font-weight: 500;
}

.btn-delete {
  @include button-style($danger-color, white);
  padding: 8px 16px;
  font-size: 14px;
}
</style>