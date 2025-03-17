<template>
  <div class="container">
    <div class="card">
      <h1 class="page-title">Главная страница</h1>

      <!-- Блок для авторизованного пользователя -->
      <div v-if="user" class="user-section">
        <p class="welcome-message">
          Добро пожаловать, <strong>{{ user.first_name }} {{ user.last_name }}</strong>!
        </p>

        <!-- Кнопка выхода -->
        <button @click="logout" class="btn btn-logout">Выйти</button>

        <!-- Список сессий -->
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

      <!-- Блок для неавторизованного пользователя -->
      <div v-else class="auth-links">
        <nuxt-link to="/login" class="btn btn-login">Войти</nuxt-link>
        <nuxt-link to="/registration" class="btn btn-register">Зарегистрироваться</nuxt-link>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

// Реактивные данные пользователя и сессий
const user = ref(null);
const sessions = ref([]);

// Загрузка данных пользователя и сессий
const fetchData = async () => {
  try {
    // Загрузка данных пользователя
    const userResponse = await $fetch('/api/users/me');
    user.value = userResponse;

    // Загрузка списка сессий
    const sessionsResponse = await $fetch('/api/users/sessions');
    sessions.value = sessionsResponse;
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error);
    // Если произошла ошибка, сбрасываем состояние пользователя
    user.value = null;
    sessions.value = [];
  }
};

// Вызов функции загрузки данных при монтировании компонента
onMounted(fetchData);

// Удаление сессии
const deleteSession = async (sessionId) => {
  console.log('Удаление сессии с ID:', sessionId);
  try {
    await $fetch('/api/users/sessions', {
      method: 'DELETE',
      query: { session_id: sessionId },
    });

    const isCurrentSessionDeleted = sessions.value.some(
      (session) => session.session_id === sessionId && session.is_current
    );

    // Обновляем список сессий
    sessions.value = sessions.value.filter(session => session.session_id !== sessionId);

    if (isCurrentSessionDeleted) {
      // Очищаем куки
      document.cookie = 'access_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      document.cookie = 'refresh_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      document.cookie = 'session_id=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';

      // Сбрасываем состояние пользователя
      user.value = null;

      // Перенаправляем на главную страницу
      await router.push('/');
    }
  } catch (error) {
    console.error('Ошибка при удалении сессии:', error);
    alert('Ошибка при удалении сессии');
  }
};

// Выход из аккаунта
const logout = async () => {
  try {
    await $fetch('/api/users/logout', {
      method: 'POST',
    });

    // Очищаем куки
    document.cookie = 'access_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
    document.cookie = 'refresh_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
    document.cookie = 'session_id=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';

    // Сбрасываем состояние пользователя
    user.value = null;

    // Перенаправляем на главную страницу
    await router.push('/');
  } catch (error) {
    console.error('Ошибка при выходе:', error);
    alert('Ошибка при выходе из аккаунта');
  }
};

// Форматирование даты
const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleString();
};

// Функция для проверки активности текущей сессии
const checkSessionActivity = () => {
  const currentSession = sessions.value.find(session => session.is_current);
  if (currentSession) {
    const lastUpdate = new Date(currentSession.last_update);
    const now = new Date();
    const diffInMinutes = (now - lastUpdate) / (1000 * 60); //минуты минуты минуты

    // Если прошло больше 15 минут, считаем сессию неактивной
    if (diffInMinutes > 15) {
      // Очищаем куки
      document.cookie = 'access_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      document.cookie = 'refresh_token=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      document.cookie = 'session_id=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';

      // Сбрасываем состояние пользователя
      user.value = null;

      // Перенаправляем на главную страницу
      router.push('/');
    }
  }
};



// Очистка интервала при уничтожении компонента
onBeforeUnmount(() => {
  clearInterval(interval);
});
</script>

<style scoped lang="scss">
@use '~/assets/styles/main.scss';
</style>git status