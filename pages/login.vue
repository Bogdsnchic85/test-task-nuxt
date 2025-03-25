<template>
  <div class="auth-page">
    <div class="auth-card">
      <h1 class="auth-title">Вход</h1>
      <!-- Форма входа - теперь с обработкой состояния загрузки -->
      <form @submit.prevent="loginUser" class="auth-form">
        <input
          v-model="formData.login"
          type="text"
          placeholder="Логин"
          class="auth-input"
          required
        />
        <input
          v-model="formData.password"
          type="password"
          placeholder="Пароль"
          class="auth-input"
          required
        />
        <button type="submit" class="auth-button" :disabled="loading">
          {{ loading ? 'Входим...' : 'Войти' }}
        </button>
      </form>
      <p class="auth-link">
        Нет аккаунта?
        <!-- Ссылка на регистрацию -->
        <NuxtLink to="/registration" class="auth-link-text">Зарегистрируйтесь</NuxtLink>
      </p>
    </div>
  </div>
</template>

<script setup>
// Состояния формы 
const formData = ref({
  login: '',
  password: ''
})

// Состояние загрузки - чтобы пользователь видел, что что-то происходит...
const loading = ref(false)

// Логика входа - переработанная и улучшенная
const loginUser = async () => {
  loading.value = true // Показываем загрузку
  try {
    await $fetch('/api/users/login', {
      method: 'POST',
      body: formData.value // Отправляем весь объект
    })
    await navigateTo('/') // Перенаправляем на главную
  } catch (error) {
    console.error('Ошибка при входе:', error) 
    // Показываем ошибку пользователю 
    alert(error.data?.message || 'Ошибка при входе')
  } finally {
    loading.value = false // Убираем индикатор загрузки в любом случае
  }
}
</script>

<style scoped lang="scss">
@use '~/assets/styles/auth.scss'; 
</style>