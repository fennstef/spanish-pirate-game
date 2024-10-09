<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  gameData: any,
  answeredQuestions: Set<number>
}>()

const emit = defineEmits<{
  (e: 'complete', points: number): void,
  (e: 'answer-question', index: number): void
}>()

const currentQuestionIndex = ref(0)
const userAnswer = ref('')
const feedback = ref('')

const unansweredQuestions = computed(() => 
  props.gameData.verbQuestions.filter((_, index) => !props.answeredQuestions.has(index))
)

const currentQuestion = computed(() => unansweredQuestions.value[currentQuestionIndex.value])

const checkAnswer = () => {
  if (currentQuestion.value) {
    if (userAnswer.value === currentQuestion.value.answer) {
      feedback.value = '¡Arrr, correcto, marinero! (Arrr, correct, sailor!)'
      emit('complete', 10)
      emit('answer-question', props.gameData.verbQuestions.indexOf(currentQuestion.value))
    } else {
      feedback.value = `¡Rayos y centellas! La respuesta correcta es "${currentQuestion.value.answer}". (Shiver me timbers! The correct answer is "${currentQuestion.value.answer}".)`
      emit('complete', 0)
    }
    
    // Move to next unanswered question or loop back to the first
    currentQuestionIndex.value = (currentQuestionIndex.value + 1) % unansweredQuestions.value.length
    userAnswer.value = ''
  }
}
</script>

<template>
  <div v-if="currentQuestion" class="placeholder-game">
    <h3>Desafío de Verbos Piratas</h3>
    <p>{{ currentQuestion.question }}</p>
    <div class="options">
      <button
        v-for="option in currentQuestion.options"
        :key="option"
        @click="userAnswer = option; checkAnswer()"
      >
        {{ option }}
      </button>
    </div>
    <p v-if="feedback" :class="{ 'correct': feedback.includes('correcto'), 'incorrect': feedback.includes('Rayos') }">
      {{ feedback }}
    </p>
  </div>
  <div v-else class="placeholder-game">
    <p>¡Has completado todos los desafíos de verbos! (You've completed all verb challenges!)</p>
  </div>
</template>

<style scoped>
/* ... (previous styles remain the same) ... */
</style>