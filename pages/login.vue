<template>
  <div class="auth-page">
    <div class="auth-card">
      <h1 class="auth-title">Вход</h1>
      <form @submit.prevent="loginUser" class="auth-form">
        <input
          v-model="login"
          type="text"
          placeholder="Логин"
          class="auth-input"
          required
        />
        <input
          v-model="password"
          type="password"
          placeholder="Пароль"
          class="auth-input"
          required
        />
        <button type="submit" class="auth-button">Войти</button>
      </form>
      <p class="auth-link">
        Нет аккаунта?
        <nuxt-link to="/registration" class="auth-link-text">Зарегистрируйтесь</nuxt-link>
      </p>
    </div>
  </div>
</template>

<script setup>
// Импорты
import { ref } from 'vue'

// Состояния
const login = ref('')
const password = ref('')

// Логика входа
const loginUser = async () => {
  try {
    const user = await $fetch('/api/users/login', {
      method: 'POST',
      body: { login: login.value, password: password.value },
    })

    // Перенаправляем на главную страницу
    navigateTo('/')
  } catch (error) {
    console.error('Ошибка при входе:', error)
    alert(error.data?.message || 'Ошибка при входе')
  }
}
</script>

<style scoped lang="scss">
@use '~/assets/styles/auth.scss';
</style>