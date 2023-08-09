<template>
  <div class="summary">
    <h1>Congratulations! 🎉</h1>
    <div class="summary__content">
      <div class="summary__question-amount">Questions: {{ amount }}</div>
      <div class="summary__correct">Correct: {{ correct }}</div>
      <div class="summary__wrong">Wrong: {{ wrong }}</div>
      <div class="summary__percentage">Success rate: {{ percentage }}%</div>
      <div class="summary__time">Time: {{ time }}s</div>
    </div>
    <button class="button--start" @click="quizRestart">Again?</button>
  </div>
</template>
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'SummaryComponent',
  props: {
    amount: Number,
    correct: Number,
    wrong: Number,
    percentage: String,
    time: Number
  },
  methods: {
    quizRestart() {
      window.location.reload()
    }
  }
})
</script>
