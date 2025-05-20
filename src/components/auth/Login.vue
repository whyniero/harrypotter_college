<script setup>
import { reactive } from 'vue'
import { defineProps, defineEmits } from 'vue'
import hashPassword from '@/utils/hashPassword.js'

const userFields = reactive({
  login: '',
  password: ''
})

const props = defineProps({
  userKey: String
})

const emit = defineEmits(['loggedin'])

async function login() {
  const key = userFields.login // Используем логин как ключ
  const data = localStorage.getItem(key)
  if (!data) {
    console.log('Данные не найдены для ключа:', key)
    alert('Пользователь с таким логином не найден. Проверьте логин или зарегистрируйтесь.')
    return
  }
  const user = JSON.parse(data)
  const hashedPassword = await hashPassword(userFields.password)
  console.log('Введённый логин:', userFields.login, 'Хешированный пароль:', hashedPassword)
  console.log('Сохранённые данные:', user)
  if (userFields.login === user.login && hashedPassword === user.password) {
    localStorage.setItem('lastUserKey', key)
    emit('loggedin', user)
  } else {
    alert('Неправильный логин или пароль. Проверьте данные.')
  }
}
</script>

<template>
  <form class="auth-form" @submit.prevent="login">
    <h2>Вход</h2>
    <div class="form-group">
      <label>Логин</label>
      <input type="text" v-model="userFields.login" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Пароль</label>
      <input type="password" v-model="userFields.password" class="form-input" required>
    </div>
    <button type="submit" class="submit-btn">Войти</button>
  </form>
</template>

<style scoped>
.auth-form {
  max-width: 400px;
  margin: 0 auto;
  padding: 24px;
}

.auth-form h2 {
  margin-bottom: 24px;
  font-size: 1.5rem;
  text-align: center;
  color: #333;
}

.form-group {
  margin-bottom: 16px;
}

.form-group label {
  display: block;
  margin-bottom: 6px;
  font-size: 0.9rem;
  color: #555;
}

.form-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 0.95rem;
}

.submit-btn {
  width: 100%;
  padding: 10px;
  background-color: #4a6fa5;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.submit-btn:hover {
  background-color: #3a5a8f;
}
</style>