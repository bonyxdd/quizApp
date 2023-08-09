<template>
  <div v-if="!showSummary" class="question">
    <h2>{{ currentQuestion.question }}</h2>
    <ul>
      <li v-for="(answer, index) in currentQuestion.answers" :key="index">
        <button
          @click="selectAnswer(index)"
          :disabled="buttonDisabled()"
          :class="buttonClass(index)"
        >
          {{ answer }}
        </button>
      </li>
    </ul>
    <button
      class="button--next-question"
      v-if="showNextButton"
      @click="validateAnswer"
      :disabled="showNextButtonDisabled"
    >
      {{ nextButtonText }}
    </button>
    <h2 class="feedback" v-if="showAnswerFeedback">{{ showAnswerFeedback }}</h2>
  </div>
  <Summary
    v-if="showSummary"
    :amount="totalQuestions"
    :correct="correct"
    :wrong="wrong"
    :percentage="((correct / (correct + wrong)) * 100).toFixed(2)"
    :time="totalTime / 1000"
  />
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue'
import type { Question } from '../App.vue'
import Summary from './Summary.vue'
import type { PropType } from 'vue'

export default defineComponent({
  name: 'QuestionComponent',
  components: { Summary },
  props: { questions: { type: Array as PropType<Question[]>, required: true } },
  setup(props) {
    const startTime = ref(0),
      totalTime = ref(0),
      questionIndex = ref(0),
      showSummary = ref(false),
      showNextButton = ref(false),
      selectedAnswerIndex = ref<number | null>(null),
      correct = ref(0),
      wrong = ref(0),
      showAnswerFeedback = ref(''),
      totalQuestions = props.questions.length

    const handleStart = () => {
      startTime.value = performance.now()
      showSummary.value = false
      questionIndex.value = 0
    }
    const currentQuestion = computed(() => props.questions[questionIndex.value])

    const buttonDisabled = (): boolean => showAnswerFeedback.value !== ''

    const buttonClass = (index: number) => {
      if (index === currentQuestion.value.correctIndex && showAnswerFeedback.value !== '')
        return 'button--correct'
      if (index !== currentQuestion.value.correctIndex && showAnswerFeedback.value !== '')
        return 'button--wrong'
    }

    const selectAnswer = (index: number) => {
      selectedAnswerIndex.value = index
      showNextButton.value = true
    }

    const validateAnswer = () => {
      if (selectedAnswerIndex.value !== null) {
        const isCorrect = selectedAnswerIndex.value === currentQuestion.value.correctIndex
        isCorrect ? correct.value++ : wrong.value++
        showAnswerFeedback.value = isCorrect ? 'correct' : 'wrong'
        userShowFeedback()
      }
    }

    const userShowFeedback = () => {
      setTimeout(() => {
        showAnswerFeedback.value = ''
        showNextButton.value = false
        selectedAnswerIndex.value = null
        nextQuestion()
      }, 1000)
    }

    const nextQuestion = () => {
      if (props.questions.length - 1 === questionIndex.value) {
        const endTime = performance.now()
        totalTime.value = endTime - startTime.value
        showSummary.value = true
      } else {
        questionIndex.value++
        selectedAnswerIndex.value = null
      }
      showNextButton.value = false
    }

    const showNextButtonDisabled = computed(
      () => selectedAnswerIndex.value === null || showAnswerFeedback.value !== ''
    )

    const nextButtonText = computed(() => {
      const currentQuestionIndex = questionIndex.value + 1
      return currentQuestionIndex === totalQuestions
        ? 'Finish'
        : `Next Question (${currentQuestionIndex}/${totalQuestions})`
    })

    return {
      currentQuestion,
      showNextButton,
      showSummary,
      selectAnswer,
      nextQuestion,
      correct,
      wrong,
      showAnswerFeedback,
      validateAnswer,
      showNextButtonDisabled,
      selectedAnswerIndex,
      buttonDisabled,
      nextButtonText,
      totalQuestions,
      totalTime,
      handleStart,
      buttonClass
    }
  }
})
</script>
