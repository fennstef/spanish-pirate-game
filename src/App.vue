<script setup lang="ts">
import { ref, onMounted } from 'vue'
import GameContainer from './components/GameContainer.vue'
import gameData from './game-data.json'
import backgroundImage from './assets/ship-background.jpg'

const score = ref(0)
const completedLocations = ref(new Set<string>())
const gameLocations = ref(gameData.locations)

onMounted(() => {
  shuffleQuestions()
})

function shuffleQuestions() {
  gameLocations.value.forEach(location => {
    location.sentenceTemplates = getRandomQuestions(location.sentenceTemplates, 3)
  })
}

function getRandomQuestions(questions: any[], count: number) {
  const shuffled = [...questions].sort(() => 0.5 - Math.random())
  return shuffled.slice(0, count)
}
</script>

<template>
  <div class="app-container" :style="{ backgroundImage: `url(${backgroundImage})` }">
    <h1>El Tesoro Pirata: Una Aventura Española</h1>
    <GameContainer
      :gameLocations="gameLocations"
      :completedLocations="completedLocations"
      :score="score"
      @update-score="(points) => score += points"
      @complete-location="(locationId) => completedLocations.add(locationId)"
    />
  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: 'Pirata One', cursive, Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  min-height: 100vh;
  color: #e0e0e0;
}

.app-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  padding: 2rem;
  box-sizing: border-box;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

.app-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: -1;
}

h1 {
  color: #ffd700;
  font-size: 2.5em;
  margin-bottom: 1em;
  text-align: center;
}
</style>