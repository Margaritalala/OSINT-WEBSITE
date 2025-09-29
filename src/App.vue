<template>
  <div class="container">
    <h1>OSINT Lab Activities – Answer Submission</h1>

    <section>
      <h2>Activity 1 – Profile Cross‑Check</h2>
      <label>Explanation:</label>
      <textarea v-model="a1"></textarea>
      <button @click="checkA1">Check Answer</button>
      <p class="feedback" :style="{ color: a1Color }">{{ a1Feedback }}</p>
    </section>

    <section>
      <h2>Activity 2 – Hashtag & Sentiment Analysis</h2>
      <label>Summary:</label>
      <textarea v-model="a2"></textarea>
      <button @click="checkA2">Check Answer</button>
      <p class="feedback" :style="{ color: a2Color }">{{ a2Feedback }}</p>
    </section>

    <section>
      <h2>Activity 3 - Identify Password Hints</h2>
      <label>Summary:</label>
      <textarea v-model="a3"></textarea>
      <button @click="checkA3">Check Answer</button>
      <p class="feedback" :style="{ color: a3Color }">{{ a3Feedback }}</p>
    </section>

    <section>
      <h2>Activity 4 – Username Cross‑Platform Search</h2>
      <label>Summary:</label>
      <textarea v-model="a4"></textarea>
      <button @click="checkA4">Check Answer</button>
      <p class="feedback" :style="{ color: a4Color }">{{ a4Feedback }}</p>
    </section>

    <section>
      <h2>Activity 5 – Social Network Mapping</h2>
      <label>Summary:</label>
      <textarea v-model="a5"></textarea>
      <button @click="checkA5">Check Answer</button>
      <p class="feedback" :style="{ color: a5Color }">{{ a5Feedback }}</p>
    </section>

    <section>
      <h2>Activity 6 – Spot Misinformation</h2>
      <label>Summary:</label>
      <textarea v-model="a6"></textarea>
      <button @click="checkA6">Check Answer</button>
      <p class="feedback" :style="{ color: a6Color }">{{ a6Feedback }}</p>
    </section>

    <section>
      <h2>Activity 7 – Data Archiving & Documentation</h2>
      <label>Summary:</label>
      <textarea v-model="a7"></textarea>
      <button @click="checkA7">Check Answer</button>
      <p class="feedback" :style="{ color: a7Color }">{{ a7Feedback }}</p>
    </section>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const a1 = ref('')
const a1Feedback = ref('')
const a1Color = ref('black')

const a2 = ref('')
const a2Feedback = ref('')
const a2Color = ref('black')

const a3 = ref('')
const a3Feedback = ref('')
const a3Color = ref('black')

const a4 = ref('')
const a4Feedback = ref('')
const a4Color = ref('black')

const a5 = ref('')
const a5Feedback = ref('')
const a5Color = ref('black')

const a6 = ref('')
const a6Feedback = ref('')
const a6Color = ref('black')

const a7 = ref('')
const a7Feedback = ref('')
const a7Color = ref('black')

const normalize = (str) => str.toLowerCase()

function checkA1() {
  a1Feedback.value = 'Answer:'
  a1Color.value = 'green'
}

function checkA2() {
  a2Feedback.value = 'Answer:'
  a2Color.value = 'green'
}

function checkA3() {
  a3Feedback.value = 'Answer:'
  a3Color.value = 'green'
}

function checkA4() {
  a4Feedback.value = 'Answer:'
  a4Color.value = 'green'
}

function checkA5() {
  a5Feedback.value = 'Answer:'
  a5Color.value = 'green'
}

function checkA6() {
  a6Feedback.value = 'Answer:'
  a6Color.value = 'green'
}

function checkA7() {
  a7Feedback.value = 'Answer:'
  a7Color.value = 'green'
}
</script>

<style>
.container {
  font-family: Arial, sans-serif;
  margin: 2rem;
  line-height: 1.5;
}
section {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #e1e1e1;
  border-radius: 4px;
}
label {
  display: block;
  margin: 0.5rem 0 0.25rem;
  font-weight: bold;
}
textarea {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}
button {
  margin-top: 0.5rem;
  padding: 0.5rem 1rem;
  background-color: #3498db;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.feedback {
  margin-top: 0.5rem;
  font-weight: bold;
}
</style>
