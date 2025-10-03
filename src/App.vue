<template>
  <div class="app-container">
    <header>
      <h1>OSINT Workshop</h1>
      <nav>
        <button @click="activeView = 'basic'" :class="{ active: activeView === 'basic' }">Basic OSINT Activities</button>
        <button @click="activeView = 'password'" :class="{ active: activeView === 'password' }">Password Challenges</button>
      </nav>
    </header>

    <main>
      <BasicOsint v-if="activeView === 'basic'" />
      <PasswordChallenges v-if="activeView === 'password'" />
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import BasicOsint from './components/BasicOsint.vue'
import PasswordChallenges from './components/PasswordChallenges.vue'

const activeView = ref('basic')
</script>

<style>
.app-container {
  font-family: Arial, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 30px;
  border-bottom: 2px solid #eee;
  padding-bottom: 20px;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

nav {
  display: flex;
  justify-content: center;
  gap: 20px;
}

nav button {
  padding: 10px 20px;
  background-color: #f8f9fa;
  border: 1px solid #ddd;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.3s;
}

nav button:hover {
  background-color: #e9ecef;
}

nav button.active {
  background-color: #3498db;
  color: white;
  border-color: #3498db;
}

main {
  min-height: 500px;
}
</style>