<template>
  <div class="password-challenges">
    <div class="pirate-header">
      <div class="pirate-title">
        <h1>⚓ Secret Code Treasure Hunt 🏴‍☠️</h1>
        <p>Crack the secret codes to unlock the pirate treasure!</p>
      </div>
    </div>

    <div class="progress-bar">
      <div class="progress" :style="{ width: progressPercentage + '%' }"></div>
    </div>
    <div class="level-info">
      <h2>Treasure Map Level {{ currentLevel }}</h2>
      <p>Levels conquered: {{ completedLevels }}/{{ totalLevels }}</p>
    </div>

    <div class="challenge-container">
      <div class="challenge-description">
        <h3>{{ currentChallenge.title }}</h3>
        <p>{{ currentChallenge.description }}</p>
        <div class="difficulty-badge" :class="getDifficultyClass(currentChallenge.difficulty)">
          <i :class="getDifficultyIcon(currentChallenge.difficulty)"></i>
          {{ currentChallenge.difficulty }}
        </div>
        <div v-if="currentChallenge.hint" class="hint">
          <button @click="showHint = !showHint">
            {{ showHint ? 'Hide' : 'Show' }} Treasure Map
          </button>
          <p v-if="showHint">{{ currentChallenge.hint }}</p>
        </div>
      </div>

      <div class="password-input">
        <div class="input-wrapper">
          <input 
            v-model="passwordGuess" 
            :type="showPassword ? 'text' : 'password'"
            placeholder="Enter secret code..."
            @keyup.enter="checkPassword"
          >
          <button @click="showPassword = !showPassword" class="toggle-password">
            <i :class="showPassword ? 'fas fa-eye-slash' : 'fas fa-eye'"></i>
          </button>
        </div>
        <button @click="checkPassword" class="submit-btn">Crack the Code 🏴‍☠️</button>
      </div>

      <div class="feedback" :class="feedbackClass">
        <i :class="feedbackType === 'success' ? 'fas fa-treasure-chest' : 'fas fa-skull-crossbones'"></i>
        {{ feedback }}
      </div>

      <!-- Explanation Display -->
      <div v-if="showExplanation" class="explanation">
        <h4><i class="fas fa-treasure-chest"></i> Treasure Revealed!</h4>
        <p>{{ currentChallenge.explanation }}</p>
      </div>

      <!-- Social Islands Section -->
      <div class="tabs">
        <div class="tab-content">
          <h4>Social Media Islands to Investigate</h4>
          <div class="profile-cards">
            <div v-for="(profile, index) in currentChallenge.profiles" :key="index" class="profile-card">
              <h5>{{ profile.platform }}</h5>
              <p><strong>Captain:</strong> {{ profile.username }}</p>
              <p><strong>Island:</strong> {{ profile.platform }}</p>
              <a :href="profile.url" target="_blank" class="profile-link">Set Sail →</a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="level-selector">
      <h3>Select Your Treasure Island:</h3>
      <div class="level-buttons">
        <button 
          v-for="level in totalLevels" 
          :key="level"
          @click="selectLevel(level)"
          :class="{ 
            active: currentLevel === level, 
            completed: completedLevels >= level
          }"
        >
          <div class="level-info-btn">
            <span class="level-number">🏝️ Level {{ level }}</span>
            <span class="level-difficulty">{{ challenges[level - 1].difficulty }}</span>
            <i v-if="completedLevels >= level" class="fas fa-treasure-chest completed-icon"></i>
          </div>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const currentLevel = ref(1)
const passwordGuess = ref('')
const feedback = ref('')
const feedbackType = ref('')
const showHint = ref(false)
const showPassword = ref(false)
const completedLevels = ref(0)
const failedAttempts = ref(0)
const showExplanation = ref(false)
const totalLevels = ref(10)

const challenges = ref([
  {
    level: 1,
    title: "Personal Treasure Gathering",
    description: "The target uses basic personal treasure in their secret code. Look for pet names and important dates.",
    password: "Fluffy2020!",
    hint: "Check their Instagram for pet information and Twitter for important dates. Secret code combines pet name + year + special character.",
    explanation: "The secret code is 'Fluffy2020!' because: 1) Instagram post shows pet named 'Fluffy' 2) Twitter post mentions year 2020 3) Common pattern adds special character at end. This demonstrates how attackers gather personal treasure from social media to construct secret codes based on pets and significant years.",
    difficulty: "Easy",
    profiles: [
      {
        platform: "Instagram",
        username: "alex_wanderer",
        url: "https://www.instagram.com/alex_wanderer"
      },
      {
        platform: "Twitter",
        username: "alex_tech",
        url: "https://www.twitter.com/alex_tech"
      }
    ]
  },
  {
    level: 2,
    title: "Treasure Map Recognition",
    description: "The target uses a specific character replacement pattern. Can you decode their secret code formula?",
    password: "S@nFr@n#2021",
    hint: "The target replaces 'a' with '@' and adds a special character before the year. Look for island-related posts.",
    explanation: "The secret code is 'S@nFr@n#2021' because: 1) Target lives in San Francisco (posts about moving there in 2021) 2) Uses leetspeak: 'a' becomes '@' 3) Adds '#' before year. This shows how attackers identify location-based patterns and common character substitution techniques.",
    difficulty: "Easy",
    profiles: [
      {
        platform: "Facebook",
        username: "sarah.j.adventures",
        url: "https://www.facebook.com/sarah.j.adventures"
      },
      {
        platform: "LinkedIn",
        username: "sarah-johnson-tech",
        url: "https://www.linkedin.com/in/sarah-johnson-tech"
      }
    ]
  },
  {
    level: 3,
    title: "Interest-Based Secret Codes",
    description: "The target combines their hobby with personal numbers. Their passion is clearly visible online.",
    password: "ChessKing42!",
    hint: "Look for their favorite game/hobby and their lucky number. The format is [Hobby][Rank][LuckyNumber]!",
    explanation: "The secret code is 'ChessKing42!' because: 1) Reddit shows chess enthusiasm with 'King' level achievement 2) Lucky number 42 mentioned multiple times 3) Exclamation mark added. This demonstrates how attackers exploit hobbies and personal numbers that people publicly share.",
    difficulty: "Medium",
    profiles: [
      {
        platform: "Reddit",
        username: "chess_master_42",
        url: "https://www.reddit.com/user/chess_master_42"
      },
      {
        platform: "Twitch",
        username: "chess_streamer",
        url: "https://www.twitch.tv/chess_streamer"
      }
    ]
  },
  {
    level: 4,
    title: "Security Question Bypass",
    description: "The target's secret code is based on security question answers. Find the treasure scattered across posts.",
    password: "M0m@N@me!1995",
    explanation: "The secret code is 'M0m@N@me!1995' because: 1) Facebook post reveals mother's maiden name 'Williams' 2) Birth year 1995 mentioned 3) Uses leetspeak: o→0, a→@ 4) Special character pattern. This shows how attackers bypass security questions using social engineering and publicly available family treasure.",
    hint: "Look for mother's maiden name and birth year. Uses leetspeak (o->0, a->@) and special characters.",
    difficulty: "Medium",
    profiles: [
      {
        platform: "Facebook",
        username: "mike.smith.family",
        url: "https://www.facebook.com/mike.smith.family"
      },
      {
        platform: "Instagram",
        username: "mike_smith_photos",
        url: "https://www.instagram.com/mike_smith_photos"
      }
    ]
  },
  {
    level: 5,
    title: "Multi-Element Combination",
    description: "This secret code combines nickname, hobby, and birth month with character substitutions.",
    password: "B3@chB0y!Surf#07",
    hint: "Nickname with leetspeak + hobby + birth month. Look for beach posts and birthday celebrations.",
    explanation: "The secret code is 'B3@chB0y!Surf#07' because: 1) Nickname 'Beach Boy' from Instagram 2) Hobby 'Surf' from TikTok 3) Birth month July (07) 4) Leetspeak: e→3, o→0 5) Special character separators. This demonstrates complex secret code construction from multiple personal elements.",
    difficulty: "Hard",
    profiles: [
      {
        platform: "Instagram",
        username: "beach_life_jason",
        url: "https://www.instagram.com/beach_life_jason"
      },
      {
        platform: "TikTok",
        username: "surfing_jason",
        url: "https://www.tiktok.com/@surfing_jason"
      }
    ]
  },
  {
    level: 6,
    title: "Reverse Engineering",
    description: "The target uses a reversed word pattern with numbers. Think backwards!",
    password: "1retsaM!@#",
    hint: "The secret code is a common word spelled backwards with numbers and special characters. Look for their favorite things.",
    explanation: "The secret code is '1retsaM!@#' because: 1) 'Master' spelled backwards 2) Number 1 at front (favorite number) 3) Special characters !@# 4) All from posts about loving 'Master' puzzles and number 1. This shows how attackers identify wordplay and pattern-based secret codes.",
    difficulty: "Hard",
    profiles: [
      {
        platform: "Reddit",
        username: "puzzle_master",
        url: "https://www.reddit.com/user/puzzle_master"
      },
      {
        platform: "GitHub",
        username: "codemaster",
        url: "https://www.github.com/codemaster"
      }
    ]
  },
  {
    level: 7,
    title: "Keyboard Pattern",
    description: "The target uses a keyboard pattern combined with personal treasure.",
    password: "Qwerty!Lisa1998",
    hint: "Look for keyboard patterns and combine with spouse/partner name and anniversary year.",
    explanation: "The secret code is 'Qwerty!Lisa1998' because: 1) 'Qwerty' keyboard pattern 2) Spouse name 'Lisa' from Facebook posts 3) Anniversary year 1998 4) Special character separator. This demonstrates how attackers identify keyboard patterns and relationship-based treasure.",
    difficulty: "Hard",
    profiles: [
      {
        platform: "Facebook",
        username: "john.lisa.couple",
        url: "https://www.facebook.com/john.lisa.couple"
      },
      {
        platform: "Instagram",
        username: "john_and_lisa",
        url: "https://www.instagram.com/john_and_lisa"
      }
    ]
  },
  {
    level: 8,
    title: "Phonetic Substitution",
    description: "The target uses phonetic replacements and number codes. Sound it out!",
    password: "4S@leB3st!2022",
    hint: "Uses number/letter phonetics (4=For, 3=E) and special characters. Look for business posts.",
    explanation: "The secret code is '4S@leB3st!2022' because: 1) 'For Sale' becomes '4S@le' (phonetic) 2) 'Best' becomes 'B3st' (leetspeak) 3) Year 2022 from business posts 4) Special characters. This shows how attackers crack phonetic and number-based secret code patterns.",
    difficulty: "Expert",
    profiles: [
      {
        platform: "LinkedIn",
        username: "entrepreneur_dave",
        url: "https://www.linkedin.com/in/entrepreneur_dave"
      },
      {
        platform: "Twitter",
        username: "business_dave",
        url: "https://www.twitter.com/business_dave"
      }
    ]
  },
  {
    level: 9,
    title: "Cultural Reference",
    description: "The target combines cultural references with personal treasure. Think pop culture!",
    password: "MayThe4thBeWithYou!1984",
    hint: "Look for favorite movies/shows and birth year. This is a famous movie quote + birth year.",
    explanation: "The secret code is 'MayThe4thBeWithYou!1984' because: 1) Star Wars quote 'May the 4th be with you' 2) Birth year 1984 from posts 3) Exclamation mark 4) Cultural reference + personal treasure. This demonstrates how attackers exploit pop culture knowledge combined with personal treasure.",
    difficulty: "Expert",
    profiles: [
      {
        platform: "Reddit",
        username: "starwars_fan84",
        url: "https://www.reddit.com/user/starwars_fan84"
      },
      {
        platform: "Twitter",
        username: "jedimaster",
        url: "https://www.twitter.com/jedimaster"
      }
    ]
  },
  {
    level: 10,
    title: "Master Treasure Hunt",
    description: "This target uses multiple layers: leetspeak, foreign words, keyboard patterns, and personal treasure.",
    password: "L337H@x0r!C0d3R#2023",
    hint: "Combines leetspeak (L337=Leet), keyboard patterns, and job title with year. Think like a pirate hacker!",
    explanation: "The secret code is 'L337H@x0r!C0d3R#2023' because: 1) 'L337' = 'Leet' (hacker speak) 2) 'H@x0r' = 'Hacker' (leetspeak) 3) 'C0d3R' = 'Coder' (leetspeak) 4) Job year 2023 5) Special character separators. This represents the ultimate combination of multiple secret code cracking techniques: leetspeak, job-related terms, and temporal treasure.",
    difficulty: "Master",
    profiles: [
      {
        platform: "GitHub",
        username: "elite_coder",
        url: "https://www.github.com/elite_coder"
      },
      {
        platform: "Stack Overflow",
        username: "haxor_coder",
        url: "https://www.stackoverflow.com/users/haxor_coder"
      }
    ]
  }
])

const progressPercentage = computed(() => {
  return (completedLevels.value / totalLevels.value) * 100
})

const currentChallenge = computed(() => {
  return challenges.value.find(challenge => challenge.level === currentLevel.value)
})

const feedbackClass = computed(() => {
  if (feedbackType.value === 'success') {
    return 'success'
  } else if (feedbackType.value === 'error') {
    return 'error'
  }
  return ''
})

onMounted(() => {
  const savedProgress = localStorage.getItem('osintPasswordProgress')
  if (savedProgress) {
    completedLevels.value = parseInt(savedProgress)
  }
})

function checkPassword() {
  if (passwordGuess.value === currentChallenge.value.password) {
    feedback.value = "Treasure found! You've cracked the secret code!"
    feedbackType.value = "success"
    failedAttempts.value = 0
    showExplanation.value = false

    if (completedLevels.value < currentLevel.value) {
      completedLevels.value = currentLevel.value
      localStorage.setItem('osintPasswordProgress', completedLevels.value.toString())
    }
    
    setTimeout(() => {
      if (currentLevel.value < totalLevels.value) {
        selectLevel(currentLevel.value + 1)
      } else {
        feedback.value = "Congratulations! You've conquered all treasure islands!"
      }
    }, 2000)
  } else {
    failedAttempts.value++
    
    if (failedAttempts.value >= 5) {
      feedback.value = "You've reached 5 attempts! Here's the treasure map:"
      feedbackType.value = "error"
      showExplanation.value = true
      
      if (completedLevels.value < currentLevel.value) {
        completedLevels.value = currentLevel.value
        localStorage.setItem('osintPasswordProgress', completedLevels.value.toString())
      }
    } else {
      feedback.value = `Wrong code, matey! Try again! (${failedAttempts.value}/5 attempts)`
      feedbackType.value = "error"
    }
  }
  
  passwordGuess.value = ''
}

function selectLevel(level) {
  currentLevel.value = level
  feedback.value = ''
  feedbackType.value = ''
  showHint.value = false
  passwordGuess.value = ''
  failedAttempts.value = 0
  showExplanation.value = false
}

function getDifficultyClass(difficulty) {
  const classes = {
    'Easy': 'easy',
    'Medium': 'medium',
    'Hard': 'hard',
    'Expert': 'expert',
    'Master': 'master'
  }
  return classes[difficulty] || 'easy'
}

function getDifficultyIcon(difficulty) {
  const icons = {
    'Easy': 'fas fa-telescope',
    'Medium': 'fas fa-compass',
    'Hard': 'fas fa-skull-crossbones',
    'Expert': 'fas fa-lock',
    'Master': 'fas fa-treasure-chest'
  }
  return icons[difficulty] || 'fas fa-lock'
}
</script>

<style scoped>
.password-challenges {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
  min-height: 100vh;
  color: #fff;
}

.pirate-header {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 15px;
  border: 2px solid #8b4513;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
}

.pirate-title h1 {
  color: #ffd700;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8);
  margin-bottom: 10px;
  font-size: 2.5em;
}

.pirate-title p {
  color: #f4e4c1;
  font-size: 1.2em;
  font-style: italic;
}

.progress-bar {
  width: 100%;
  height: 25px;
  background-color: #2c1810;
  border-radius: 12px;
  margin-bottom: 20px;
  overflow: hidden;
  border: 2px solid #8b4513;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.5);
}

.progress {
  height: 100%;
  background: linear-gradient(90deg, #ffd700, #ff8c00);
  transition: width 0.5s ease;
  box-shadow: 0 0 10px rgba(255, 215, 0, 0.5);
}

.level-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding: 15px;
  background: rgba(139, 69, 19, 0.3);
  border-radius: 10px;
  border: 1px solid #8b4513;
}

.level-info h2 {
  color: #ffd700;
  margin: 0;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.challenge-container {
  background-color: rgba(0, 0, 0, 0.4);
  border-radius: 15px;
  padding: 25px;
  margin-bottom: 30px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
  border: 2px solid #8b4513;
  backdrop-filter: blur(5px);
}

.challenge-description h3 {
  color: #ffd700;
  margin-bottom: 10px;
  font-size: 1.5em;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.challenge-description p {
  color: #f4e4c1;
  margin-bottom: 15px;
  line-height: 1.6;
}

.difficulty-badge {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 0.85em;
  font-weight: 600;
  margin-right: 10px;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.difficulty-badge.easy {
  background-color: rgba(40, 167, 69, 0.3);
  color: #90ee90;
  border-color: #28a745;
}

.difficulty-badge.medium {
  background-color: rgba(255, 193, 7, 0.3);
  color: #ffd700;
  border-color: #ffc107;
}

.difficulty-badge.hard {
  background-color: rgba(220, 53, 69, 0.3);
  color: #ff6b6b;
  border-color: #dc3545;
}

.difficulty-badge.expert {
  background-color: rgba(108, 117, 125, 0.3);
  color: #adb5bd;
  border-color: #6c757d;
}

.difficulty-badge.master {
  background-color: rgba(139, 69, 19, 0.5);
  color: #ffd700;
  border-color: #8b4513;
}

.hint {
  margin-top: 15px;
}

.hint button {
  background-color: #8b4513;
  color: #ffd700;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
}

.hint button:hover {
  background-color: #a0522d;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.4);
}

.hint p {
  margin-top: 10px;
  padding: 12px;
  background-color: rgba(255, 215, 0, 0.1);
  border-radius: 6px;
  border-left: 4px solid #ffd700;
  color: #f4e4c1;
  border: 1px solid rgba(255, 215, 0, 0.3);
}

.password-input {
  display: flex;
  flex-direction: column;
  margin: 20px 0;
  gap: 15px; 
}

.input-wrapper {
  position: relative;
}

.input-wrapper input {
  width: 90%;
  padding: 12px 45px 12px 15px;
  border: 2px solid #8b4513;
  border-radius: 8px;
  font-size: 16px;
  transition: all 0.3s;
  background-color: rgba(0, 0, 0, 0.3);
  color: #fff;
}

.input-wrapper input:focus {
  outline: none;
  border-color: #ffd700;
  box-shadow: 0 0 10px rgba(255, 215, 0, 0.3);
}

.input-wrapper input::placeholder {
  color: rgba(244, 228, 193, 0.6);
}

.toggle-password {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  color: #f4e4c1;
  cursor: pointer;
  padding: 5px;
}

.toggle-password:hover {
  color: #ffd700;
}

.submit-btn {
  padding: 12px 24px;
  background: linear-gradient(135deg, #8b4513, #a0522d);
  color: #ffd700;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.3s;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.submit-btn:hover {
  background: linear-gradient(135deg, #a0522d, #8b4513);
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.5);
}

.feedback {
  padding: 12px;
  border-radius: 6px;
  margin-bottom: 20px;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 10px;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.feedback.success {
  background-color: rgba(40, 167, 69, 0.2);
  color: #90ee90;
  border-color: #28a745;
}

.feedback.error {
  background-color: rgba(220, 53, 69, 0.2);
  color: #ff6b6b;
  border-color: #dc3545;
}

.explanation {
  background-color: rgba(255, 215, 0, 0.1);
  border: 1px solid #ffd700;
  border-radius: 6px;
  padding: 15px;
  margin-bottom: 20px;
}

.explanation h4 {
  color: #ffd700;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.explanation p {
  color: #f4e4c1;
  line-height: 1.6;
  margin: 0;
}

.tabs {
  margin-top: 25px;
}

.tab-buttons {
  display: flex;
  border-bottom: 2px solid #8b4513;
  margin-bottom: 20px;
}

.tab-buttons button {
  background: none;
  border: none;
  padding: 12px 20px;
  cursor: pointer;
  font-weight: 600;
  color: #f4e4c1;
  border-bottom: 2px solid transparent;
  transition: all 0.3s;
}

.tab-buttons button.active {
  color: #ffd700;
  border-bottom-color: #ffd700;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.tab-buttons button:hover {
  color: #ffd700;
}

.tab-content {
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.tab-content h4 {
  color: #ffd700;
  margin-bottom: 15px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.instructor-note {
  background-color: rgba(255, 193, 7, 0.1);
  border: 1px solid #ffc107;
  border-radius: 6px;
  padding: 12px;
  margin-bottom: 15px;
  color: #ffd700;
  display: flex;
  align-items: center;
  gap: 10px;
}

.profile-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 15px;
}

.profile-card {
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
  border-left: 4px solid #ffd700;
  transition: all 0.3s;
  border: 1px solid rgba(255, 215, 0, 0.3);
}

.profile-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.5);
  border-left-color: #ff8c00;
}

.profile-card h5 {
  color: #ffd700;
  margin-bottom: 10px;
  font-size: 1.1em;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.profile-card p {
  margin-bottom: 8px;
  color: #f4e4c1;
}

.profile-link {
  display: inline-block;
  margin-top: 10px;
  padding: 8px 16px;
  background-color: #8b4513;
  color: #ffd700;
  text-decoration: none;
  border-radius: 6px;
  font-weight: 600;
  transition: all 0.3s;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
}

.profile-link:hover {
  background-color: #a0522d;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.4);
}

.post-cards {
  display: grid;
  gap: 15px;
  margin-top: 15px;
}

.post-card {
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
  border-left: 4px solid #ff8c00;
  border: 1px solid rgba(255, 140, 0, 0.3);
}

.post-platform {
  background-color: rgba(139, 69, 19, 0.5);
  color: #ffd700;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.8em;
  font-weight: 600;
  margin-bottom: 8px;
  display: inline-block;
}

.post-content {
  color: #f4e4c1;
  line-height: 1.5;
}

.level-selector {
  margin-top: 30px;
  background-color: rgba(0, 0, 0, 0.4);
  border-radius: 15px;
  padding: 25px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
  border: 2px solid #8b4513;
  backdrop-filter: blur(5px);
}

.level-selector h3 {
  color: #ffd700;
  margin-bottom: 15px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.level-buttons {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 15px;
  margin-top: 15px;
}

.level-buttons button {
  padding: 15px;
  border: 2px solid #8b4513;
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s;
  position: relative;
  overflow: hidden;
  color: #f4e4c1;
}

.level-buttons button:hover:not(:disabled) {
  background-color: rgba(139, 69, 19, 0.5);
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.4);
  border-color: #ffd700;
}

.level-buttons button.active {
  background-color: rgba(139, 69, 19, 0.7);
  color: #ffd700;
  border-color: #ffd700;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.level-buttons button.completed {
  background-color: rgba(40, 167, 69, 0.3);
  color: #90ee90;
  border-color: #28a745;
}

.level-info-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}

.level-number {
  font-weight: 700;
  font-size: 1.1em;
  color: #ffd700;
}

.level-difficulty {
  font-size: 0.8em;
  opacity: 0.8;
}

.completed-icon {
  position: absolute;
  top: 5px;
  right: 5px;
  color: #ffd700;
  font-size: 1.2em;
}

@media (max-width: 768px) {
  .password-challenges {
    padding: 10px;
  }
  
  .level-info {
    flex-direction: column;
    text-align: center;
    gap: 10px;
  }
  
  .password-input {
    flex-direction: column;
  }
  
  .input-wrapper input {
    border-radius: 8px;
  }
  
  .submit-btn {
    border-radius: 8px;
  }
   
  .level-buttons {
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  }
  
  .profile-cards {
    grid-template-columns: 1fr;
  }
  
  .pirate-title h1 {
    font-size: 2em;
  }
}
</style>