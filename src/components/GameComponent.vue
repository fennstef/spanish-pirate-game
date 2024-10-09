<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import QuestionDialog from './QuestionDialog.vue'

const props = defineProps<{
  gameLocations: any[],
  completedLocations: Set<string>,
  score: number
}>()

const emit = defineEmits<{
  (e: 'updateScore', points: number): void,
  (e: 'completeLocation', locationId: string): void
}>()

const currentLocationId = ref('')

const currentLocation = computed(() => props.gameLocations.find(loc => loc.id === currentLocationId.value))

function navigateToLocation(locationId: string) {
  currentLocationId.value = locationId
}

onMounted(() => {
  // Start with El Galeón
  if (props.gameLocations.length > 0) {
    navigateToLocation(props.gameLocations[0].id)
  }
})
</script>

<template>
  <div class="game-component">
    <div class="location-buttons">
      <button
        v-for="location in gameLocations"
        :key="location.id"
        @click="navigateToLocation(location.id)"
        :class="{ 
          current: location.id === currentLocationId, 
          completed: completedLocations.has(location.id)
        }"
      >
        {{ location.name }}
      </button>
    </div>
    <QuestionDialog
      v-if="currentLocation"
      :location="currentLocation"
      :completedLocations="completedLocations"
      @update-score="(points) => emit('updateScore', points)"
      @complete-location="(locationId) => emit('completeLocation', locationId)"
    />
  </div>
</template>

<style scoped>
.game-component {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 800px;
}

.location-buttons {
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
  cursor: pointer;
  transition: border-color 0.25s;
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