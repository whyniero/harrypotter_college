<script setup>
const emit = defineEmits(['close'])
</script>

<template>
  <div class="overlay" @click.self="emit('close')">
    <div class="modal">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
}

.modal {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  width: max-content;
  max-height: 90vh;
  overflow-y: auto;
  padding: 20px;
}
</style>