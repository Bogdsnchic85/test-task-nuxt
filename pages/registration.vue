<template>
  <div class="auth-page">
    <div class="auth-card">
      <h1 class="auth-title">Регистрация</h1>
      <!-- Форма регистрации - с индикатором загрузки -->
      <form @submit.prevent="register" class="auth-form">
        <input
          v-model="formData.first_name"
          type="text"
          placeholder="Имя"
          class="auth-input"
          required
        />
        <input
          v-model="formData.last_name"
          type="text"
          placeholder="Фамилия"
          class="auth-input"
          required
        />
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
          {{ loading ? 'Регистрируем...' : 'Зарегистрироваться' }}
        </button>
      </form>
      <p class="auth-link">
        Уже есть аккаунт?
        <!-- Ссылка на вход -->
        <NuxtLink to="/login" class="auth-link-text">Войдите</NuxtLink>
      </p>
    </div>
  </div>
</template>

<script setup>
// Состояния формы 
const formData = ref({
  first_name: '',
  last_name: '',
  login: '',
  password: ''
})

// Индикатор загрузки - чтобы форма не дёргалась
const loading = ref(false)

// Логика регистрации 
const register = async () => {
  loading.value = true // Показываем загрузку
  try {
    await $fetch('/api/users/registration', {
      method: 'POST',
      body: formData.value // Отправляем весь объект
    })
    // После успешной регистрации - на страницу входа
    await navigateTo('/login')
  } catch (error) {
    console.error('Ошибка при регистрации:', error) 
    // Показываем ошибку 
    alert(error.data?.message || 'Ошибка при регистрации')
  } finally {
    loading.value = false 
  }
}
</script>

<style scoped lang="scss">
@use '~/assets/styles/auth.scss'; 
</style>