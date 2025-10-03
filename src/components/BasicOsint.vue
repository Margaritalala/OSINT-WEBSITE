<template>
    <div class="osint-challenges">
      <div class="pirate-header">
        <div class="pirate-title">
          <h1>⚓ OSINT Treasure Hunt 🏴‍☠️</h1>
          <p>Set sail on a digital adventure to uncover hidden treasures!</p>
        </div>
      </div>
      
      <div class="progress-bar">
        <div class="progress" :style="{ width: progressPercentage + '%' }"></div>
      </div>
      <div class="level-info">
        <h2>Treasure Map</h2>
        <p>Islands explored: {{ completedActivities }}/{{ totalActivities }}</p>
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
  
        <div class="answer-input">
          <label>{{ currentChallenge.questionLabel }}</label>
          <textarea 
            v-model="answerGuess" 
            :placeholder="currentChallenge.placeholder"
            rows="4"
          ></textarea>
          <button @click="checkAnswer" class="submit-btn">Dig for Treasure 🏴‍☠️</button>
        </div>
  
        <div class="feedback" :class="feedbackClass">
          <i :class="feedbackType === 'success' ? 'fas fa-treasure-chest' : 'fas fa-skull-crossbones'"></i>
          {{ feedback }}
        </div>
  
        <!-- Solution Display -->
        <div v-if="showSolution" class="solution">
          <h4><i class="fas fa-treasure-chest"></i> Treasure Found!</h4>
          <p>{{ currentChallenge.solution }}</p>
        </div>
  
        <!-- Target Islands Section -->
        <div class="tabs">
          <div class="tab-content">
            <h4>Target Social Media Islands</h4>
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
  
      <div class="activity-selector">
        <h3>Select Your Island:</h3>
        <div class="activity-buttons">
          <button 
            v-for="challenge in challenges" 
            :key="challenge.level"
            @click="selectActivity(challenge.level)"
            :class="{ 
              active: currentActivity === challenge.level, 
              completed: completedActivities >= challenge.level
            }"
          >
            <div class="activity-info-btn">
              <span class="activity-number">🏝️ {{ challenge.level }}</span>
              <span class="activity-title">{{ challenge.shortTitle }}</span>
              <span class="activity-difficulty">{{ challenge.difficulty }}</span>
              <i v-if="completedActivities >= challenge.level" class="fas fa-treasure-chest completed-icon"></i>
            </div>
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted } from 'vue'
  
  const currentActivity = ref(1)
  const answerGuess = ref('')
  const feedback = ref('')
  const feedbackType = ref('')
  const showHint = ref(false)
  const completedActivities = ref(0)
  const showSolution = ref(false)
  const failedAttempts = ref(0)
  
  // Store answers and solutions for each level
  const studentAnswers = ref({})
  const solutionStates = ref({})
  
  const totalActivities = ref(5)
  
  const challenges = ref([
    {
      level: 1,
      title: "Island Cross-Check Analysis",
      shortTitle: "Island Cross-Check",
      description: "Analyze the target's social media islands across different seas to identify inconsistencies, fake accounts, or hidden connections.",
      questionLabel: "What inconsistencies or connections did you find between the islands?",
      placeholder: "Describe any differences in bio information, profile pictures, posting patterns, or suspicious activity...",
      expectedAnswer: ["inconsistent", "inconsistencies", "different", "mismatch", "discrepancy", "variation", "fake", "multiple", "alex", "johnson", "williams", "age", "location"],
      solution: "Key inconsistencies found: 1) Age discrepancy: Instagram bio shows '25 years old' while X shows '28 years old' 2) Name variations: Alex Johnson vs Alexander Williams vs Alex J. 3) Location mismatch: X posts about New York but Instagram shows California locations 4) Profile picture differences across platforms suggest potential identity manipulation or multiple personas. Students can identify various inconsistencies including age differences, name variations, location mismatches, or fake accounts - all valid findings.",
      hint: "Look for differences in names, birth dates, locations, profile pictures, and posting patterns across islands.",
      difficulty: "Easy",
      profiles: [
        {
          platform: "Instagram",
          username: "alex_wanderer2000",
          url: "https://www.instagram.com/alex_wanderer2000?igsh=bTlhY2wycmtqbzZ5&utm_source=qr"
        },
        {
          platform: "X (Twitter)",
          username: "alex_tech",
          url: "https://x.com/tech_alex45926"
        },
        {
          platform: "LinkedIn",
          username: "alex-johnson",
          url: "https://www.linkedin.com/in/alex-johnson-5974b1388/"
        }
      ]
    },
    {
      level: 2,
      title: "Treasure Map & Sentiment Analysis",
      shortTitle: "Treasure Map Analysis",
      description: "Analyze the target's treasure map usage and sentiment patterns to understand their interests, emotional state, and potential vulnerabilities.",
      questionLabel: "What patterns did you identify in their treasure map usage and sentiment?",
      placeholder: "Describe recurring treasure maps, emotional patterns, time-based posting behavior, or sentiment shifts...",
      expectedAnswer: ["negative", "positive", "mixed", "emotional", "vulnerable", "stressed", "anxious", "overwhelmed", "sentiment", "mood", "hashtag", "pattern"],
      solution: "Sentiment analysis reveals: 1) Predominant negative sentiment on Reddit with treasure maps #stressed #overwhelmed #anxiety 2) Contrasting positive sentiment on Instagram with #blessed #grateful #happy 3) Late-night posting patterns with #insomnia #cantSleep indicating emotional vulnerability 4) Recurring #mentalhealth #therapy themes suggest ongoing psychological challenges and potential manipulation susceptibility. Students may identify negative patterns, positive contrasts, mixed sentiments, or emotional vulnerabilities - all valid observations.",
      hint: "Look for emotional keywords, frequency of certain treasure maps, and changes in sentiment over time.",
      difficulty: "Easy",
      profiles: [
        {
          platform: "Reddit",
          username: "Mundane-Excuse-8249",
          url: "https://www.reddit.com/user/Mundane-Excuse-8249/"
        },
        {
          platform: "Instagram",
          username: "sarahmood99",
          url: "https://www.instagram.com/sarahmood99?igsh=MXdoYWM2OXlyaTNxdg%3D%3D&utm_source=qr"
        }
      ]
    },
    {
      level: 3,
      title: "Captain Name Cross-Island Search",
      shortTitle: "Captain Name Search",
      description: "Track the target's captain name variations across multiple islands to build a comprehensive digital footprint and identify hidden accounts.",
      questionLabel: "What accounts and captain name variations did you discover?",
      placeholder: "List all found accounts, captain name patterns, and any suspicious or hidden profiles...",
      expectedAnswer: ["multiple", "accounts", "username", "variations", "tech_guru_42", "techguru42", "footprint", "digital", "different", "guru", "discord", "tiktok"],
      solution: "Digital footprint mapping reveals: 1) Primary accounts: 'tech_guru_42' (Reddit, 5-year history), 'techguru42' (GitHub, same avatar) 2) Pattern variations: 'TechGuru#0042' (Discord), '@techguru_official' (TikTok brand account) 3) Abbreviated forms: 'TG42' on tech forums 4) This comprehensive mapping provides multiple attack vectors and social engineering opportunities. Students can identify multiple accounts, username patterns, or digital footprint variations - all valid findings.",
      hint: "Search for common captain name variations: adding numbers, underscores, island-specific prefixes, or alternative spellings.",
      difficulty: "Medium",
      profiles: [
        {
          platform: "Reddit",
          username: "tech_guru_42",
          url: "https://www.reddit.com/user/tech_guru_42"
        },
        {
          platform: "GitHub",
          username: "techguru42",
          url: "https://www.github.com/techguru42"
        }
      ]
    },
    {
      level: 4,
      title: "Secret Code Identification",
      shortTitle: "Secret Codes",
      description: "Identify potential secret codes and security question answers hidden in the target's social media posts and profile information.",
      questionLabel: "What potential secret codes or security answers did you discover?",
      placeholder: "List pet names, birth dates, favorite movies, security question answers, or other personal information...",
      expectedAnswer: ["pet", "pets", "max", "sparky", "password", "security", "hint", "mother", "maiden", "williams", "godfather", "march", "personal", "information"],
      solution: "Critical secret codes identified: 1) Pet information: 'Max' (current dog, 5 years), 'Sparky' (first goldfish at age 7) 2) Security question answer: Mother's maiden name is 'Williams' 3) Personal preferences: Favorite movie 'Godfather', significant date March 15th 4) These details could be used for secret code construction or security question bypass attacks. Students can identify various secret codes, security information, or personal data - all valid findings.",
      hint: "Look for posts about pets, family members, important dates, favorite movies/books, and personal milestones.",
      difficulty: "Medium",
      category: "Security Analysis",
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
      title: "Personal Treasure Gathering",
      shortTitle: "Personal Treasure",
      description: "Gather comprehensive personal treasure about the target from their social media profiles, posts, and public records. This is an advanced OSINT exercise in information collection.",
      questionLabel: "What personal treasure did you discover about the target?",
      placeholder: "List all personal details you found: full name, age, birth date, location, workplace, education, family members, interests, habits, etc...",
      expectedAnswer: ["personal", "information", "details", "name", "age", "birth", "location", "work", "education", "family", "interests", "habits", "email", "phone", "address"],
      solution: "Comprehensive personal treasure gathered: 1) Full identity: Sarah Michelle Chen, age 27, born March 15, 1997 2) Contact: sarah.chen@email.com, (555) 123-4567 3) Location: Lives in San Francisco, CA, originally from Boston, MA 4) Education: MIT Computer Science 2019, Boston Latin School 5) Employment: Software Engineer at TechCorp since 2020 6) Family: Parents Robert and Linda Chen, brother David Chen 7) Interests: Hiking, photography, Italian food, sci-fi movies 8) Habits: Gym routine, morning coffee, weekend brunch spots 9) Travel: Annual trips to Italy, recent Japan vacation 10) This demonstrates how seemingly innocent social posts can reveal extensive personal data for social engineering or identity theft.",
      hint: "Look for biographical information, location tags, work/education details, family mentions, contact information, daily routines, and personal preferences across all islands.",
      difficulty: "Hard",
      profiles: [
        {
          platform: "LinkedIn",
          username: "sarah-chen-tech",
          url: "https://www.linkedin.com/in/sarah-chen-tech"
        },
        {
          platform: "Instagram",
          username: "sarah.explores",
          url: "https://www.instagram.com/sarah.explores"
        },
        {
          platform: "Facebook",
          username: "sarah.m.chen",
          url: "https://www.facebook.com/sarah.m.chen"
        }
      ]
    }
  ])
  
  const progressPercentage = computed(() => {
    return (completedActivities.value / totalActivities.value) * 100
  })
  
  const currentChallenge = computed(() => {
    return challenges.value.find(challenge => challenge.level === currentActivity.value)
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
    const savedProgress = localStorage.getItem('osintActivityProgress')
    if (savedProgress) {
      completedActivities.value = parseInt(savedProgress)
    }
    
    // Load saved answers and solution states
    const savedAnswers = localStorage.getItem('osintStudentAnswers')
    if (savedAnswers) {
      studentAnswers.value = JSON.parse(savedAnswers)
    }
    
    const savedSolutions = localStorage.getItem('osintSolutionStates')
    if (savedSolutions) {
      solutionStates.value = JSON.parse(savedSolutions)
    }
    
    // Load current level's data
    loadLevelData()
  })
  
  function checkAnswer() {
    const normalizedAnswer = answerGuess.value.toLowerCase()
    const expectedAnswers = Array.isArray(currentChallenge.value.expectedAnswer) 
      ? currentChallenge.value.expectedAnswer.map(ans => ans.toLowerCase())
      : [currentChallenge.value.expectedAnswer.toLowerCase()]
    
    const hasCorrectKeyword = expectedAnswers.some(expected => normalizedAnswer.includes(expected))
    
    // Save student's answer
    studentAnswers.value[currentActivity.value] = answerGuess.value
    localStorage.setItem('osintStudentAnswers', JSON.stringify(studentAnswers.value))
    
    if (hasCorrectKeyword || normalizedAnswer.length > 20) {
      feedback.value = "Excellent analysis! You've successfully discovered the treasure!"
      feedbackType.value = "success"
      showSolution.value = true
      failedAttempts.value = 0
      
      // Save solution state
      solutionStates.value[currentActivity.value] = true
      localStorage.setItem('osintSolutionStates', JSON.stringify(solutionStates.value))
      
      if (completedActivities.value < currentActivity.value) {
        completedActivities.value = currentActivity.value
        localStorage.setItem('osintActivityProgress', completedActivities.value.toString())
      }
    } else {
      failedAttempts.value++
      
      if (failedAttempts.value >= 5) {
        feedback.value = "You've reached 5 attempts! Here's the treasure map:"
        feedbackType.value = "error"
        showSolution.value = true
        
        // Save solution state
        solutionStates.value[currentActivity.value] = true
        localStorage.setItem('osintSolutionStates', JSON.stringify(solutionStates.value))
        
        if (completedActivities.value < currentActivity.value) {
          completedActivities.value = currentActivity.value
          localStorage.setItem('osintActivityProgress', completedActivities.value.toString())
        }
      } else {
        feedback.value = `Keep searching, matey! Try again! (${failedAttempts.value}/5 attempts)`
        feedbackType.value = "error"
      }
    }
  }
  
  function selectActivity(activity) {
    // Save current level's data before switching
    saveCurrentLevelData()
    
    // Switch to new level
    currentActivity.value = activity
    
    // Load new level's data
    loadLevelData()
  }
  
  function saveCurrentLevelData() {
    studentAnswers.value[currentActivity.value] = answerGuess.value
    solutionStates.value[currentActivity.value] = showSolution.value
    localStorage.setItem('osintStudentAnswers', JSON.stringify(studentAnswers.value))
    localStorage.setItem('osintSolutionStates', JSON.stringify(solutionStates.value))
  }
  
  function loadLevelData() {
    answerGuess.value = studentAnswers.value[currentActivity.value] || ''
    showSolution.value = solutionStates.value[currentActivity.value] || false
    feedback.value = ''
    feedbackType.value = ''
    showHint.value = false
    failedAttempts.value = 0
  }
  
  function getDifficultyClass(difficulty) {
    const classes = {
      'Easy': 'easy',
      'Medium': 'medium',
      'Hard': 'hard',
      'Expert': 'expert'
    }
    return classes[difficulty] || 'easy'
  }
  
  function getDifficultyIcon(difficulty) {
    const icons = {
      'Easy': 'fas fa-telescope',
      'Medium': 'fas fa-compass',
      'Hard': 'fas fa-skull-crossbones',
      'Expert': 'fas fa-treasure-chest'
    }
    return icons[difficulty] || 'fas fa-telescope'
  }
  </script>
  
  <style scoped>
  .osint-challenges {
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
  
  .answer-input {
    margin: 20px 0;
  }
  
  .answer-input label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: #ffd700;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
  }
  
  .answer-input textarea {
    width: 100%;
    padding: 12px 15px;
    border: 2px solid #8b4513;
    border-radius: 8px;
    font-size: 16px;
    font-family: inherit;
    resize: vertical;
    min-height: 120px;
    transition: all 0.3s;
    background-color: rgba(0, 0, 0, 0.3);
    color: #fff;
  }
  
  .answer-input textarea:focus {
    outline: none;
    border-color: #ffd700;
    box-shadow: 0 0 10px rgba(255, 215, 0, 0.3);
  }
  
  .answer-input textarea::placeholder {
    color: rgba(244, 228, 193, 0.6);
  }
  
  .submit-btn {
    margin-top: 10px;
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
  
  .solution {
    background-color: rgba(255, 215, 0, 0.1);
    border: 1px solid #ffd700;
    border-radius: 6px;
    padding: 15px;
    margin-bottom: 20px;
  }
  
  .solution h4 {
    color: #ffd700;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    gap: 8px;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
  }
  
  .solution p {
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
  
  .activity-selector {
    margin-top: 30px;
    background-color: rgba(0, 0, 0, 0.4);
    border-radius: 15px;
    padding: 25px;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
    border: 2px solid #8b4513;
    backdrop-filter: blur(5px);
  }
  
  .activity-selector h3 {
    color: #ffd700;
    margin-bottom: 15px;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
  }
  
  .activity-buttons {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 15px;
    margin-top: 15px;
  }
  
  .activity-buttons button {
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
  
  .activity-buttons button:hover:not(:disabled) {
    background-color: rgba(139, 69, 19, 0.5);
    transform: translateY(-2px);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.4);
    border-color: #ffd700;
  }
  
  .activity-buttons button.active {
    background-color: rgba(139, 69, 19, 0.7);
    color: #ffd700;
    border-color: #ffd700;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
  }
  
  .activity-buttons button.completed {
    background-color: rgba(40, 167, 69, 0.3);
    color: #90ee90;
    border-color: #28a745;
  }
  
  .activity-info-btn {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 5px;
  }
  
  .activity-number {
    font-weight: 700;
    font-size: 1.1em;
    color: #ffd700;
  }
  
  .activity-title {
    font-weight: 600;
    font-size: 0.9em;
    text-align: center;
  }
  
  .activity-difficulty {
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
    .osint-challenges {
      padding: 10px;
    }
    
    .level-info {
      flex-direction: column;
      text-align: center;
      gap: 10px;
    }
    
    .activity-buttons {
      grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    }
    
    .profile-cards {
      grid-template-columns: 1fr;
    }
    
    .pirate-title h1 {
      font-size: 2em;
    }
  }
  </style>