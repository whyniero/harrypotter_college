<script setup>
import { onMounted, ref } from 'vue'
import Login from '@/components/auth/Login.vue'
import Register from '@/components/auth/Register.vue'
import CharactersList from '@/components/characters/CharactersList.vue'
import Modal from '@/UI/Modal.vue'
import Search from '@/components/characters/Search.vue'
import FavoritesCounter from '@/components/characters/FavoritesCounter.vue'

const userKey = ref(null)
const currentUser = ref(null)
const isRegisterOpen = ref(false)
const isLoginOpen = ref(false)
const characters = ref([])
const filteredCharacters = ref([])

async function fetchCharacters() {
  try {
    const res = await fetch('https://hp-api.onrender.com/api/characters')
    characters.value = await res.json()
    filteredCharacters.value = characters.value
  } catch (err) {
    console.error(err)
  }
}

function setCurrentUser() {
  const savedKey = localStorage.getItem('lastUserKey')
  if (savedKey) {
    userKey.value = savedKey
    const userData = localStorage.getItem(savedKey)
    if (userData) {
      currentUser.value = JSON.parse(userData)
    }
  }
}

onMounted(async () => {
  await fetchCharacters()
  setCurrentUser()
})

function handleRegistered(userData) {
  userKey.value = userData.login
  currentUser.value = userData
  if (!currentUser.value.favorites) {
    currentUser.value.favorites = []
  }
  localStorage.setItem(userKey.value, JSON.stringify(currentUser.value))
  localStorage.setItem('lastUserKey', userKey.value)
  isRegisterOpen.value = false
}

function handleLoggedIn(userData) {
  userKey.value = userData.login
  currentUser.value = userData
  if (!currentUser.value.favorites) {
    currentUser.value.favorites = []
  }
  localStorage.setItem(userKey.value, JSON.stringify(currentUser.value))
  isLoginOpen.value = false
}

function toggleRegisterModal() {
  isRegisterOpen.value = !isRegisterOpen.value
  if (isRegisterOpen.value) isLoginOpen.value = false
}

function toggleLoginModal() {
  isLoginOpen.value = !isLoginOpen.value
  if (isLoginOpen.value) isRegisterOpen.value = false
}

function logout() {
  currentUser.value = null
  userKey.value = null
  localStorage.removeItem('lastUserKey')
}

function updateFilteredCharacters(filtered) {
  filteredCharacters.value = filtered
}

function toggleFavorite(characterId) {
  if (!currentUser.value) return
  const favorites = currentUser.value.favorites
  const index = favorites.indexOf(characterId)
  if (index === -1) {
    favorites.push(characterId)
  } else {
    favorites.splice(index, 1)
  }
  localStorage.setItem(userKey.value, JSON.stringify(currentUser.value))
}
</script>

<template>
  <div class="container">
    <div class="panel">
      <div class="search-wrapper">
        <Search :characters="characters" @filter="updateFilteredCharacters" />
      </div>
      <div class="buttons">
        <template v-if="!currentUser">
          <button @click="toggleRegisterModal">Зарегистрироваться</button>
          <button @click="toggleLoginModal">Войти</button>
        </template>
        <div v-if="currentUser" class="username">
          {{ currentUser.firstname }} {{ currentUser.surname }} {{ currentUser.lastname }}
          <button v-if="currentUser" @click="logout">Выйти</button>
        </div>
      </div>
    </div>
    <FavoritesCounter v-if="currentUser" :favorites="currentUser.favorites" :characters="characters" />
    <Modal v-if="isRegisterOpen" @close="toggleRegisterModal">
      <Register @registered="handleRegistered" />
    </Modal>
    <Modal v-if="isLoginOpen" @close="toggleLoginModal">
      <Login :userKey="userKey" @loggedin="handleLoggedIn" />
    </Modal>
    <CharactersList
      :characters="filteredCharacters"
      :isLoggedIn="!!currentUser"
      :favorites="currentUser ? currentUser.favorites : []"
      @toggleFavorite="toggleFavorite"
    />
  </div>
</template>

<style scoped>
.container {
  padding: 10px 20px;
  margin: 0 auto;
  max-width: 1400px;
}

.username {
  font-weight: 600;
  color: white;
}

.panel {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  background: linear-gradient(to left, #350101, #70000e);
  position: fixed;
  left: 0;
  top: 0;
  z-index: 999;
  padding: 12px 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  transition: opacity 0.3s;
}

.search-wrapper {
  display: flex;
  align-items: center;
  background: white;
  border-radius: 12px;
  padding: 6px 12px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.buttons {
  display: flex;
  justify-content: space-between;
  gap: 10px;
}

button {
  padding: 8px 16px;
  background: #4a90e2;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  cursor: pointer;
  transition: background 0.3s, transform 0.2s;
}

button:hover {
  background: #357abd;
  transform: translateY(-2px);
}

button:last-child {
  background: #ff6b6b;
}

button:last-child:hover {
  background: #e63946;
}

</style>