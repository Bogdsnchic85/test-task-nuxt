<template>
  <div class="auth-page">
    <div class="auth-card">
      <h1 class="auth-title">Регистрация</h1>
      <form @submit.prevent="register" class="auth-form">
        <input
          v-model="first_name"
          type="text"
          placeholder="Имя"
          class="auth-input"
          required
        />
        <input
          v-model="last_name"
          type="text"
          placeholder="Фамилия"
          class="auth-input"
          required
        />
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
        <button type="submit" class="auth-button">Зарегистрироваться</button>
      </form>
      <p class="auth-link">
        Уже есть аккаунт?
        <nuxt-link to="/login" class="auth-link-text">Войдите</nuxt-link>
      </p>
    </div>
  </div>
</template>

<script setup>
// Импорты
import { ref } from 'vue'

// Состояния
const first_name = ref('')
const last_name = ref('')
const login = ref('')
const password = ref('')

// Логика регистрации
const register = async () => {
  try {
    await $fetch('/api/users/registration', {
      method: 'POST',
      body: {
        first_name: first_name.value,
        last_name: last_name.value,
        login: login.value,
        password: password.value,
      },
    })
    navigateTo('/login')
  } catch (error) {
    alert(error.data?.message || 'Ошибка при регистрации')
  }
}
</script>

<style scoped lang="scss">
@use '~/assets/styles/auth.scss';
</style>