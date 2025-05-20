<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  characters: Array
})

const emit = defineEmits(['filter'])

const searchQuery = ref('')
const sortField = ref('')
const sortOrder = ref('asc')
const selectedGender = ref('')
const selectedHouse = ref('')

function resetFilters() {
  searchQuery.value = ''
  sortField.value = ''
  sortOrder.value = 'asc'
  selectedGender.value = ''
  selectedHouse.value = ''
}

function parseDate(dateStr) {
  if (!dateStr) return null
  const [month, day, year] = dateStr.split('-').map(Number)
  const date = new Date(year, month - 1, day)
  return isNaN(date.getTime()) ? null : date
}

const filteredAndSortedCharacters = computed(() => {
  let result = [...props.characters]

  // поиск
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    result = result.filter(char =>
      char.name?.toLowerCase().includes(query) ||
      char.dateOfBirth?.toLowerCase().includes(query) ||
      char.species?.toLowerCase().includes(query)
    )
  }

  // фильтрация
  result = result.filter(char =>
    (!selectedGender.value || char.gender === selectedGender.value) &&
    (!selectedHouse.value || char.house?.toLowerCase() === selectedHouse.value)
  )

  // сортировка
  if (sortField.value) {
    result.sort((a, b) => {
      let aValue = a[sortField.value] || ''
      let bValue = b[sortField.value] || ''
      if (sortField.value === 'dateOfBirth') {
        const aDate = parseDate(a.dateOfBirth)
        const bDate = parseDate(b.dateOfBirth)
        // если у одного из персонажей дата отсутствует, помещаем его в конец
        if (!aDate && !bDate) return 0
        if (!aDate) return sortOrder.value === 'asc' ? 1 : -1
        if (!bDate) return sortOrder.value === 'asc' ? -1 : 1
        return sortOrder.value === 'asc' ? aDate - bDate : bDate - aDate
      }
      if (aValue < bValue) return sortOrder.value === 'asc' ? -1 : 1
      if (aValue > bValue) return sortOrder.value === 'asc' ? 1 : -1
      return 0
    })
  }

  return result
})

watch(filteredAndSortedCharacters, (newValue) => {
  emit('filter', newValue)
}, { immediate: true })
</script>

<template>
  <div class="search-content">
    <button @click="resetFilters">Сбросить</button>
    <select v-model="sortField">
      <option disabled value="">Сортировать по</option>
      <option value="name">Имени</option>
      <option value="dateOfBirth">Дате рождения</option>
      <option value="species">Виду</option>
    </select>
    <select :disabled="sortField === ''" v-model="sortOrder">
      <option value="asc">По возрастанию</option>
      <option value="desc">По убыванию</option>
    </select>
    <select v-model="selectedGender">
      <option value="">Пол: все</option>
      <option value="male">Мужской</option>
      <option value="female">Женский</option>
    </select>
    <select v-model="selectedHouse">
      <option value="">Факультет: все</option>
      <option value="gryffindor">Гриффиндор</option>
      <option value="slytherin">Слизерин</option>
      <option value="ravenclaw">Когтевран</option>
      <option value="hufflepuff">Пуффендуй</option>
    </select>
    <input
      type="text"
      v-model="searchQuery"
      placeholder="Поиск по имени, дате рождения, виду"
    />
  </div>
</template>

<style scoped>
.search-content {
  display: flex;
  align-items: center;
  gap: 8px;
  width: 100%;
}

input {
  width: 300px;
}

input, select {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 8px;
  font-size: 0.9rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s;
}

input:focus, select:focus {
  border-color: #4a90e2;
  outline: none;
}

input {
  flex: 1;
  min-width: 200px;
}

button {
  border: none;
  border-radius: 8px;
  background-color: #ff6b6b;
  color: white;
  padding: 8px 12px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #e63946;
}
</style>