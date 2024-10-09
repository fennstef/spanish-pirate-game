<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  location: any,
  completedLocations: Set<string>
}>()

const emit = defineEmits<{
  (e: 'updateScore', points: number): void
  (e: 'completeLocation', locationId: string): void
}>()

const currentQuestionIndex = ref(0)
const answeredQuestions = ref(new Set<number>())
const selectedAnswer = ref('')
const feedback = ref('')

const currentQuestion = computed(() => {
  if (props.location && props.location.sentenceTemplates && props.location.sentenceTemplates.length > currentQuestionIndex.value) {
    return generateQuestion(props.location.sentenceTemplates[currentQuestionIndex.value])
  }
  return null
})

const isLocationCompleted = computed(() => answeredQuestions.value.size === 3) // We ask 3 questions per location

function generateQuestion(template: string) {
  // Implement the question generation logic here
  // This should return an object with 'sentence', 'correctAnswer', and 'options' properties
  // For now, we'll return a placeholder object
  return {
    sentence: template,
    correctAnswer: 'placeholder',
    options: ['placeholder', 'option2', 'option3', 'option4']
  }
}

function checkAnswer() {
  if (currentQuestion.value) {
    if (selectedAnswer.value === currentQuestion.value.correctAnswer) {
      feedback.value = '¡Arrr, correcto, marinero!'
      answeredQuestions.value.add(currentQuestionIndex.value)
      
      if (isLocationCompleted.value) {
        emit('updateScore', 10 * 3) // 10 points per question, 3 questions
        emit('completeLocation', props.location.id)
      } else {
        emit('updateScore', 10)
      }
    } else {
      feedback.value = `¡Rayos y centellas! La respuesta correcta es "${currentQuestion.value.correctAnswer}".`
      // Reset answered questions for this location
      answeredQuestions.value.clear()
    }
    
    if (!isLocationCompleted.value) {
      // Move to next unanswered question
      do {
        currentQuestionIndex.value = (currentQuestionIndex.value + 1) % 3
      } while (answeredQuestions.value.has(currentQuestionIndex.value))
    }
    
    selectedAnswer.value = ''
  }
}
</script>

<template>
  <div class="question-dialog">
    <h2>{{ location.name }}</h2>
    <p>{{ location.description }}</p>
    <div v-if="currentQuestion && !isLocationCompleted" class="question">
      <p>{{ currentQuestion.sentence }}</p>
      <div class="options">
        <button
          v-for="option in currentQuestion.options"
          :key="option"
          @click="selectedAnswer = option; checkAnswer()"
          :class="{ selected: selectedAnswer === option }"
        >
          {{ option }}
        </button>
      </div>
      <p v-if="feedback" :class="{ 'correct': feedback.includes('correcto'), 'incorrect': feedback.includes('Rayos') }">
        {{ feedback }}
      </p>
      <p>Preguntas respondidas: {{ answeredQuestions.size }} / 3</p>
    </div>
    <div v-else-if="isLocationCompleted">
      <p>¡Has completado todas las preguntas de este lugar!</p>
    </div>
  </div>
</template>

<style scoped>
.question-dialog {
  background-color: rgba(0, 0, 0, 0.7);
  border-radius: 10px;
  padding: 1em;
  margin-top: 1em;
}

.question {
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 5px;
  padding: 1em;
  margin-top: 1em;
}

.options {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.5em;
  margin-top: 1em;
}

.correct {
  color: #4CAF50;
}

.incorrect {
  color: #f44336;
}

button.selected {
  background-color: #ffd700;
  color: #4a4a4a;
}
</style>