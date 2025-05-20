<script setup>
import { computed } from 'vue'

const props = defineProps({
  favorites: Array,
  characters: Array
})

const wizardCount = computed(() => {
  return props.favorites.filter(id => {
    const character = props.characters.find(c => c.id === id)
    return character && character.wizard
  }).length
})

const nonWizardCount = computed(() => {
  return props.favorites.filter(id => {
    const character = props.characters.find(c => c.id === id)
    return character && !character.wizard
  }).length
})
</script>

<template>
  <div class="favorites-counter">
    <h4>Избранное:</h4>
    <p>Волшебники: {{ wizardCount }}</p>
    <p>Не волшебники: {{ nonWizardCount }}</p>
  </div>
</template>

<style scoped>
.favorites-counter {
  text-align: right;
  font-size: 0.9rem;
  color: #333;
  padding: 5px 10px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 8px 0 0 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  position: fixed;
  right: 0;
  top: 100px;
  z-index: 9999;
}

.favorites-counter h4 {
  font-weight: 700;
  margin-bottom: 8px;
  font-size: 1rem;
}

.favorites-counter p {
  margin: 4px 0;
}
</style>