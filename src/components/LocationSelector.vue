<script setup lang="ts">
defineProps<{
  locations: any[],
  completedLocations: Set<string>,
  currentLocationId: string
}>()

const emit = defineEmits<{
  (e: 'selectLocation', locationId: string): void
}>()
</script>

<template>
  <div class="location-selector">
    <button
      v-for="location in locations"
      :key="location.id"
      @click="emit('selectLocation', location.id)"
      :class="{ 
        current: location.id === currentLocationId, 
        completed: completedLocations.has(location.id)
      }"
    >
      {{ location.name }}
    </button>
  </div>
</template>

<style scoped>
.location-selector {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-bottom: 1em;
  gap: 0.5em;
}

button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #4a4a4a;
  color: #e0e0e0;
  cursor: pointer;
  transition: border-color 0.25s, background-color 0.25s;
}

button:hover {
  border-color: #ffd700;
}

button.current {
  background-color: #ffd700;
  color: #4a4a4a;
}

button.completed {
  background-color: #00a86b;
  color: white;
}
</style>