<template>
  <div class="container">
    <div class="card">
      <h1 class="page-title">Главная страница</h1>

      <!-- Блок для авторизованного пользователя - как раньше, но лучше -->
      <div v-if="user" class="user-section">
        <p class="welcome-message">
          Добро пожаловать, <strong>{{ user.first_name }} {{ user.last_name }}</strong>!
        </p>

        <!-- Кнопка выхода -->
        <button @click="logout" class="btn btn-logout">Выйти</button>

        <!-- Список сессий - остался прежним -->
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
      </div>

      <!-- Блок для неавторизованного пользователя  -->
      <div v-else class="auth-links">
        <NuxtLink to="/login" class="btn btn-login">Войти</NuxtLink>
        <NuxtLink to="/registration" class="btn btn-register">Зарегистрироваться</NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup>
// Импорты - теперь по-новому, как в Nuxt 3, я все-таки надеюсь что сделала правильно
import { ref, onMounted } from 'vue'

// Реактивные данные пользователя и сессий
const user = ref(null)
const sessions = ref([])

// Загрузка данных пользователя и сессий - оптимизированная версия
const fetchData = async () => {
  try {
    // Загрузка данных пользователя - теперь с useFetch
    const { data: userData } = await useFetch('/api/users/me')
    user.value = userData.value

    // Загрузка списка сессий - тоже с useFetch
    const { data: sessionsData } = await useFetch('/api/users/sessions')
    sessions.value = sessionsData.value
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
    // Если произошла ошибка, сбрасываем состояние пользователя
    user.value = null
    sessions.value = []
  }
}

// Вызов функции при монтировании 
onMounted(fetchData)

// Удаление сессии - теперь без кук, сервер сам разберется, ну по-крайней мере я надеюсь на это
const deleteSession = async (sessionId) => {
  console.log('Удаление сессии с ID:', sessionId) 
  try {
    await $fetch('/api/users/sessions', {
      method: 'DELETE',
      query: { session_id: sessionId },
    })

    // Обновляем список сессий 
    sessions.value = sessions.value.filter(session => session.session_id !== sessionId)
  } catch (error) {
    console.error('Ошибка при удалении сессии:', error)
    alert('Ошибка при удалении сессии') 
  }
}

// Выход из аккаунта 
const logout = async () => {
  try {
    await $fetch('/api/users/logout', { method: 'POST' })
    user.value = null
    await navigateTo('/') 
  } catch (error) {
    console.error('Ошибка при выходе:', error)
    alert('Ошибка при выходе из аккаунта') 
  }
}

// Форматирование даты 
const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleString() // Локализация - это важно
}
</script>

<style scoped lang="scss">
@use '~/assets/styles/main.scss'; 
</style>