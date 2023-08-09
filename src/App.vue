<template>
  <div class="app">
    <h1 v-if="!started">Frontend Quiz!</h1>
    <button class="button--start" v-if="!started" @click="startQuiz">Start</button>
    <QuestionComponent v-else :questions="shuffledQuestions" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import QuestionComponent from '@/components/QuestionComponent.vue'
import questionsData from './assets/dataSet.json'

export interface Question {
  question: string
  answers: string[]
  correctIndex: number
}

export default defineComponent({
  name: 'App',
  components: { QuestionComponent },
  computed: {
    shuffledQuestions(): Question[] {
      return this.shuffleArray(questionsData) as Question[]
    }
  },
  methods: {
    shuffleArray(array: any[]): any[] {
      let curIndex = array.length,
        randomIndex,
        tempVal
      while (curIndex != 0) {
        randomIndex = Math.floor(Math.random() * curIndex)
        curIndex--
        tempVal = array[curIndex]
        array[curIndex] = array[randomIndex]
        array[randomIndex] = tempVal
      }
      return array
    },
    startQuiz() {
      this.started = true
    }
  },
  data() {
    return {
      questions: questionsData as Question[],
      started: false
    }
  }
})
</script>
