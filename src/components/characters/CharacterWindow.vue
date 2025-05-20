<script setup>
defineProps({
  character: Object,
  isLoggedIn: Boolean,
  favorites: Array
})

defineEmits(['toggleFavorite'])
</script>

<template>
  <div class="modal-body">
    <div class="column left">
      <img
        :src="character.image || '/noimage.jpg'"
        :alt="character.name"
      />
      <h3 class="character-name">{{ character.name || 'Неизвестно' }}</h3>
      <p><strong>Факультет:</strong> {{ character.house || 'Неизвестно' }}</p>
      <p><strong>Дата рождения:</strong> {{ character.dateOfBirth || 'Неизвестно' }}</p>
      <p><strong>Пол:</strong> {{ character.gender || 'Неизвестно' }}</p>
      <p><strong>Вид:</strong> {{ character.species || 'Неизвестно' }}</p>
      <p><strong>Статус:</strong>
        {{ character.hogwartsStudent ? 'Студент' : character.hogwartsStaff ? 'Персонал' : 'Неизвестно' }}
      </p>
    </div>
    <div class="column right">
      <p><strong>Патронус:</strong> {{ character.patronus || 'Неизвестно' }}</p>
      <p><strong>Волшебная палочка:</strong>
        <span v-if="character.wand?.wood">
            {{ `${character.wand.wood}, ${character.wand.core || 'Неизвестно'}, ${character.wand.length || 'Неизвестно'} дюймов`
          }}
          </span>
        <span v-else>Неизвестно</span>
      </p>
      <p><strong>Актер:</strong> {{ character.actor || 'Неизвестно' }}</p>
      <p><strong>Родословная:</strong> {{ character.ancestry || 'Неизвестно' }}</p>
      <p><strong>Цвет глаз:</strong> {{ character.eyeColour || 'Неизвестно' }}</p>
      <p><strong>Цвет волос:</strong> {{ character.hairColour || 'Неизвестно' }}</p>
      <ul v-if="character.alternate_names.length > 0"><strong>Альтернативные имена:</strong>
        <li v-for="altName in character.alternate_names">
          {{ altName }}
        </li>
      </ul>
      <ul v-if="character.alternate_actors.length > 0"><strong>Альтернативные актеры:</strong>
        <li v-for="altAct in character.alternate_actors">
          {{ altAct }}
        </li>
      </ul>
      <p><strong>Жив:</strong> {{ character.alive ? 'Да' : 'Нет' || 'Неизвестно' }}</p>
      <p><strong>Волшебник:</strong> {{ character.wizard ? 'Да' : 'Нет' || 'Неизвестно' }}</p>

      <button
        v-if="isLoggedIn"
        class="favorite-button"
        :class="{ active: favorites.includes(character.id) }"
        @click="$emit('toggleFavorite', character.id)"
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
    </div>
  </div>
</template>

<style scoped>
.modal-body {
  display: flex;
  gap: 20px;
  padding: 20px;
  position: relative;
}

img {
  width: 100%;
  max-height: 400px;
  object-fit: cover;
  border-radius: 8px;
}

.column {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 10px;
  color: #555;
}

strong {
  font-weight: 700;
}

.character-name {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 600;
  color: #333;
}

.full-details h4 {
  margin-bottom: 10px;
  font-size: 1rem;
}

ul {
  list-style-type: disc;
  padding-left: 20px;
  margin: 5px 0;
}

li {
  margin: 5px 0;
}

.favorite-button {
  background: none;
  width: 60px;
  height: 60px;
  position: absolute;
  top: 4px;
  left: 4px;
  border: solid 3px #ceac26;
  border-radius: 10px;
  cursor: pointer;
  padding: 4px;
  color: #ccc;
  transition: .2s ease;
  box-shadow: 0 0 10px 10px rgba(0, 0, 0, 0.2);
}

.favorite-icon {
  width: 40px;
  height: 40px;
}

.favorite-button:hover {
  transform: scale(1.1);
}

.favorite-button.active {
  background-color: #ceac26;
  box-shadow: 0 0 10px 10px rgba(206, 172, 38, 0.2);
}

.favorite-button.active > .favorite-icon path {
  stroke: white;
}
</style>