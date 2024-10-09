<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  locations: any[],
  completedLocations: Set<string>,
  score: number
}>()

const totalQuestions = computed(() => {
  return props.locations.reduce((total, location) => total + (location.sentenceTemplates?.length || 0), 0)
})

const answeredQuestions = computed(() => {
  return props.locations.reduce((total, location) => {
    return total + (props.completedLocations.has(location.id) ? 3 : 0) // We ask 3 questions per location
  }, 0)
})
</script>

<template>
  <div class="task-overview">
    <h3>Progreso del Pirata</h3>
    <p>Puntuación: {{ score }}</p>
    <p>Preguntas respondidas: {{ answeredQuestions }} / {{ totalQuestions }}</p>
    <ul>
      <li v-for="location in locations" :key="location.id" :class="{ completed: completedLocations.has(location.id) }">
        {{ location.name }}
        <span v-if="completedLocations.has(location.id)"> ✓</span>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.task-overview {
  background-color: rgba(0, 0, 0, 0.7);
  border-radius: 10px;
  padding: 1em;
  width: 100%;
  max-width: 300px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 0.5em;
}

li.completed {
  color: #00a86b;
}

@media (max-width: 768px) {
  .task-overview {
    max-width: 100%;
    margin-top: 1em;
  }
}
</style>