<script setup>
import { reactive } from 'vue'
import { defineEmits } from 'vue'
import hashPassword from '@/utils/hashPassword.js'

const userFields = reactive({
  login: '',
  password: '',
  firstname: '',
  surname: '',
  lastname: '',
  phone: '',
  gender: '',
  age: '',
  favoriteHouses: []
})

const emit = defineEmits(['registered'])

function validatePhone(phone) {
  const regex = /^(\+?7|8)?\(?\d{3}\)?-?\d{3}-?\d{2}-?\d{2}$/
  return regex.test(phone)
}

async function register() {
  if (!userFields.login ||
    !userFields.password ||
    !userFields.firstname ||
    !userFields.surname ||
    !userFields.lastname ||
    !userFields.phone ||
    !userFields.gender ||
    !userFields.age ||
    userFields.favoriteHouses.length === 0
  ) {
    alert('Все поля обязательны, включая выбор хотя бы одного факультета.')
    return
  }

  if (userFields.firstname.length <= 1 ||
    userFields.surname.length <= 2 ||
    userFields.lastname.length <= 2 ||
    !/^[А-ЯЁ][а-яё]+$/.test(userFields.firstname) ||
    !/^[А-ЯЁ][а-яё]+$/.test(userFields.surname) ||
    !/^[А-ЯЁ][а-яё]+$/.test(userFields.lastname)
  ) {
    alert('ФИО должно начинаться с заглавной буквы, имя > 1 символа, фамилия и отчество > 2 символов, только кириллица.')
    return
  }

  if (!validatePhone(userFields.phone)) {
    alert('Телефон должен быть в формате 8(XXX)XXX-XX-XX или +7(XXX)XXX-XX-XX.')
    return
  }

  if (userFields.gender !== 'male' && userFields.gender !== 'female') {
    alert('Пол должен быть "male" или "female".')
    return
  }

  if (isNaN(userFields.age) || userFields.age < 10) {
    alert('Возраст должен быть числом и не менее 10 лет.')
    return
  }

  if (localStorage.getItem(userFields.login)) {
    alert('Пользователь с таким логином уже существует. Пожалуйста, выберите другой логин.')
    return
  }

  const hashedPassword = await hashPassword(userFields.password)
  const user = {
    login: userFields.login,
    password: hashedPassword,
    firstname: userFields.firstname,
    surname: userFields.surname,
    lastname: userFields.lastname,
    phone: userFields.phone,
    gender: userFields.gender,
    age: userFields.age,
    favoriteHouses: userFields.favoriteHouses,
    favorites: []
  }
  localStorage.setItem(userFields.login, JSON.stringify(user))
  localStorage.setItem('lastUserKey', userFields.login)
  emit('registered', user)
}
</script>

<template>
  <form class="auth-form" @submit.prevent="register">
    <h2>Регистрация</h2>
    <div class="form-group">
      <label>Логин</label>
      <input type="text" v-model="userFields.login" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Пароль</label>
      <input type="password" v-model="userFields.password" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Имя</label>
      <input type="text" v-model="userFields.firstname" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Фамилия</label>
      <input type="text" v-model="userFields.surname" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Отчество</label>
      <input type="text" v-model="userFields.lastname" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Телефон</label>
      <input type="text" v-model="userFields.phone" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Пол</label>
      <select v-model="userFields.gender" class="form-input" required>
        <option value="" disabled>Выберите пол</option>
        <option value="male">Мужской</option>
        <option value="female">Женский</option>
      </select>
    </div>
    <div class="form-group">
      <label>Возраст</label>
      <input type="number" v-model.number="userFields.age" class="form-input" required>
    </div>
    <div class="form-group">
      <label>Любимые факультеты</label>
      <div class="checkbox-group">
        <label><input type="checkbox" v-model="userFields.favoriteHouses" value="Gryffindor"> Гриффиндор</label>
        <label><input type="checkbox" v-model="userFields.favoriteHouses" value="Slytherin"> Слизерин</label>
        <label><input type="checkbox" v-model="userFields.favoriteHouses" value="Ravenclaw"> Когтевран</label>
        <label><input type="checkbox" v-model="userFields.favoriteHouses" value="Hufflepuff"> Пуффендуй</label>
      </div>
    </div>
    <button type="submit" class="submit-btn">Зарегистрироваться</button>
  </form>
</template>

<style scoped>
.auth-form {
  margin: 0 auto;
  padding: 4px 24px;
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
  margin-bottom: 2px;
  font-size: 0.9rem;
  color: #555;
}

.form-input {
  width: 400px;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 0.95rem;
}

.checkbox-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.checkbox-group label {
  display: flex;
  align-items: center;
  gap: 8px;
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