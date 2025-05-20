<script setup>
import { ref } from 'vue'
import Modal from '@/UI/Modal.vue'
import CharacterWindow from '@/components/characters/CharacterWindow.vue'

const props = defineProps({
  characters: Array,
  isLoggedIn: Boolean,
  favorites: Array
})

const emit = defineEmits(['toggleFavorite'])

const clickedCharacter = ref(null)
const isCharacterWindowOpen = ref(false)

function toggleCharacterWindow() {
  isCharacterWindowOpen.value = !isCharacterWindowOpen.value
}

function setClickedCharacter(character) {
  clickedCharacter.value = character
  isCharacterWindowOpen.value = true
}

function getHouseClass(house) {
  switch (house?.toLowerCase()) {
    case 'gryffindor':
      return 'gryffindor'
    case 'slytherin':
      return 'slytherin'
    case 'hufflepuff':
      return 'hufflepuff'
    case 'ravenclaw':
      return 'ravenclaw'
    default:
      return 'neutral'
  }
}
</script>

<template>
  <div class="container">
    <Modal v-if="isCharacterWindowOpen" @close="toggleCharacterWindow">
      <CharacterWindow
        :character="clickedCharacter"
        :isLoggedIn="isLoggedIn"
        :favorites="favorites"
        @toggleFavorite="emit('toggleFavorite', $event)"
      />
    </Modal>
    <div
      class="card"
      v-for="character in characters"
      :key="character.id"
      :class="getHouseClass(character.house)"
      @click="setClickedCharacter(character)"
    >
      <button
        v-if="isLoggedIn"
        class="favorite-button"
        :class="{ active: favorites.includes(character.id) }"
        @click.stop="emit('toggleFavorite', character.id)"
      >
        <svg class="favorite-icon" width="800px" height="800px" viewBox="0 0 64 64" fill="none"
             xmlns="http://www.w3.org/2000/svg">
          <g id="SVGRepo_bgCarrier" stroke-width="0" />
          <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round" />
          <g id="SVGRepo_iconCarrier">
            <path
              d="M30.051 45.6071L17.851 54.7401C17.2728 55.1729 16.5856 55.4363 15.8662 55.5008C15.1468 55.5652 14.4237 55.4282 13.7778 55.1049C13.1319 54.7817 12.5887 54.2851 12.209 53.6707C11.8293 53.0563 11.6281 52.3483 11.628 51.626V15.306C11.628 13.2423 12.4477 11.2631 13.9069 9.8037C15.3661 8.34432 17.3452 7.52431 19.409 7.52405H45.35C47.4137 7.52431 49.3929 8.34432 50.8521 9.8037C52.3112 11.2631 53.131 13.2423 53.131 15.306V51.625C53.1309 52.3473 52.9297 53.0553 52.55 53.6697C52.1703 54.2841 51.6271 54.7807 50.9812 55.1039C50.3353 55.4272 49.6122 55.5642 48.8928 55.4998C48.1734 55.4353 47.4862 55.1719 46.908 54.739L34.715 45.6071C34.0419 45.1031 33.2238 44.8308 32.383 44.8308C31.5422 44.8308 30.724 45.1031 30.051 45.6071V45.6071Z"
              stroke="#ceac26" stroke-width="4" stroke-linecap="round" stroke-linejoin="round" />
          </g>
        </svg>
      </button>
      <img
        class="character-image"
        :src="character.image || '/noimage.jpg'"
        :alt="character.name"
      />
      <div class="character-info">
        <h3 class="character-name">{{ character.name || 'Неизвестно' }}</h3>
        <div class="character-meta">
          <span v-if="character.house" class="house-tag">{{ character.house }}</span>
          <span v-if="character.species">{{ character.species }}</span>
        </div>
        <div class="character-details">
          <p><strong>Дата рождения:</strong> {{ character.dateOfBirth || 'Неизвестно' }}</p>
          <p><strong>Пол:</strong> {{ character.gender || 'Неизвестно' }}</p>
          <p><strong>Статус:</strong>
            {{ character.hogwartsStudent ? 'Студент' : character.hogwartsStaff ? 'Персонал' : 'Неизвестно' }}</p>
          <p><strong>Вид:</strong> {{ character.species || 'Неизвестно' }}</p>
          <p><strong>Патронус:</strong> {{ character.patronus || 'Неизвестно' }}</p>
          <p><strong>Волшебная палочка:</strong>
            {{ character.wand?.wood ? `${character.wand.wood},
              ${character.wand.core || 'Неизвестно'},
              ${character.wand.length || 'Неизвестно'} дюймов` : 'Неизвестно'
            }}
          </p>
          <p><strong>Актер:</strong> {{ character.actor || 'Неизвестно' }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 24px;
  padding: 16px;
  max-width: 1200px;
  margin: 0 auto;
  padding-top: 100px !important;
}

.card {
  border-radius: 8px;
  overflow: hidden;
  background: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  cursor: pointer;
  transform: translateY(0);
  transition: all 0.2s ease;
  position: relative;
}

.card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  transform: translateY(-8px);
}

.character-image {
  width: 100%;
  height: 240px;
  object-fit: cover;
  display: block;
}

.character-info {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.character-name {
  margin: 0;
  font-size: 1.1rem;
  font-weight: 600;
  color: #333;
}

.character-meta {
  display: flex;
  gap: 8px;
  font-size: 0.85rem;
  color: #666;
}

.house-tag {
  padding: 2px 8px;
  border-radius: 4px;
  font-weight: 500;
}

.character-details {
  font-size: 0.9rem;
  color: #555;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.favorite-button {
  background: rgba(0, 0, 0, 0.6);
  width: 42px;
  height: 42px;
  position: absolute;
  top: 10px;
  right: 10px;
  border: solid 2px #ceac26;
  border-radius: 10px;
  cursor: pointer;
  padding: 4px;
  color: #ccc;
  transition: .2s ease;
  box-shadow: 0 0 10px 10px rgba(0, 0, 0, 0.2);
}

.favorite-icon {
  width: 30px;
  height: 30px;
}

.favorite-button:hover {
  transform: scale(1.1);
  border: solid 2px #ceac26;
}

.favorite-button.active {
  background-color: #ceac26;
  box-shadow: 0 0 10px 10px rgba(206, 172, 38, 0.2);

}

.favorite-button.active > .favorite-icon path {
  stroke: white;
}

.gryffindor .house-tag {
  background-color: #f8e8e8;
  color: #c62828;
}

.slytherin .house-tag {
  background-color: #e8f5e9;
  color: #2e7d32;
}

.hufflepuff .house-tag {
  background-color: #fff8e1;
  color: #f9a825;
}

.ravenclaw .house-tag {
  background-color: #e3f2fd;
  color: #1565c0;
}

.neutral .house-tag {
  background-color: #f5f5f5;
  color: #666;
}
</style>