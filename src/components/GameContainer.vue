<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import LocationSelector from './LocationSelector.vue'
import QuestionDialog from './QuestionDialog.vue'
import TaskOverview from './TaskOverview.vue'

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

function updateScore(points: number) {
  emit('updateScore', points)
}

function completeLocation(locationId: string) {
  emit('completeLocation', locationId)
}

onMounted(() => {
  // Start with El Galeón
  if (props.gameLocations.length > 0) {
    currentLocationId.value = props.gameLocations[0].id
  }
})
</script>

<template>
  <div class="game-container">
    <div class="game-content">
      <LocationSelector
        :locations="gameLocations"
        :completedLocations="completedLocations"
        :currentLocationId="currentLocationId"
        @select-location="navigateToLocation"
      />
      <QuestionDialog
        v-if="currentLocation"
        :location="currentLocation"
        :completedLocations="completedLocations"
        @update-score="updateScore"
        @complete-location="completeLocation"
      />
    </div>
    <TaskOverview
      :locations="gameLocations"
      :completedLocations="completedLocations"
      :score="score"
    />
  </div>
</template>

<style scoped>
.game-container {
  display: flex;
  justify-content: space-between;
  gap: 2rem;
  width: 100%;
  max-width: 1200px;
}

.game-content {
  flex: 1;
  max-width: 800px;
  background-color: rgba(0, 0, 0, 0.7);
  padding: 1rem;
  border-radius: 10px;
}

@media (max-width: 768px) {
  .game-container {
    flex-direction: column;
  }
}
</style>