<template>
  <div class="poll-container" :class="{ 'slide-left': slideLeft, 'slide-right': slideRight }">
    <div class="score-display" v-if="quizComplete">
      Final Score: {{ score }}/{{ questions.length }} ({{ Math.round((score/questions.length) * 100) }}%)
    </div>
    <div v-else class="progress">
      Question {{ currentQuestionIndex + 1 }} of {{ questions.length }}
    </div>
    
    <h2 class="poll-question">{{ currentQuestion.question }}</h2>
    
    <div class="options-container">
      <div 
        v-for="(option, index) in currentQuestion.options" 
        :key="index"
        class="option"
        :class="{
          'voted': hasVoted,
          'correct': hasVoted && index === currentQuestion.correctAnswer,
          'incorrect': hasVoted && selectedOption === index && index !== currentQuestion.correctAnswer
        }"
        @click="!hasVoted && vote(index)"
      >
        <div class="option-content">
          <span class="option-text">{{ option }}</span>
          <transition name="fade">
            <div v-if="hasVoted" class="result-indicator">
              {{ index === currentQuestion.correctAnswer ? '✓' : (selectedOption === index ? '✗' : '') }}
            </div>
          </transition>
        </div>
      </div>
    </div>

    <div class="navigation-buttons">
      <button 
        v-if="currentQuestionIndex > 0" 
        @click="previousQuestion" 
        class="nav-button"
      >
        ← Previous
      </button>
      <button 
        v-if="hasVoted && !quizComplete" 
        @click="nextQuestion" 
        class="nav-button"
      >
        Next →
      </button>
      <button 
        v-if="quizComplete" 
        @click="restartQuiz" 
        class="nav-button restart"
      >
        Restart Quiz
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const currentQuestionIndex = ref(0)
const hasVoted = ref(false)
const selectedOption = ref(null)
const score = ref(0)
const quizComplete = ref(false)
const slideLeft = ref(false)
const slideRight = ref(false)

const questions = [
  {
    question: 'What is the capital of France?',
    options: ['London', 'Berlin', 'Paris', 'Madrid'],
    correctAnswer: 2
  },
  {
    question: 'Which planet is known as the Red Planet?',
    options: ['Venus', 'Mars', 'Jupiter', 'Saturn'],
    correctAnswer: 1
  },
  {
    question: 'What is the largest mammal in the world?',
    options: ['African Elephant', 'Blue Whale', 'Giraffe', 'Hippopotamus'],
    correctAnswer: 1
  },
  {
    question: 'Who painted the Mona Lisa?',
    options: ['Vincent van Gogh', 'Pablo Picasso', 'Leonardo da Vinci', 'Michelangelo'],
    correctAnswer: 2
  },
  {
    question: 'What is the chemical symbol for gold?',
    options: ['Ag', 'Fe', 'Au', 'Cu'],
    correctAnswer: 2
  },
  {
    question: 'Which country is home to the kangaroo?',
    options: ['New Zealand', 'South Africa', 'Australia', 'Brazil'],
    correctAnswer: 2
  },
  {
    question: 'What is the largest organ in the human body?',
    options: ['Heart', 'Brain', 'Liver', 'Skin'],
    correctAnswer: 3
  },
  {
    question: 'Who wrote "Romeo and Juliet"?',
    options: ['Charles Dickens', 'William Shakespeare', 'Jane Austen', 'Mark Twain'],
    correctAnswer: 1
  },
  {
    question: 'What is the hardest natural substance on Earth?',
    options: ['Gold', 'Iron', 'Diamond', 'Platinum'],
    correctAnswer: 2
  },
  {
    question: 'Which programming language was created by Brendan Eich?',
    options: ['Python', 'Java', 'JavaScript', 'C++'],
    correctAnswer: 2
  }
]

const currentQuestion = computed(() => questions[currentQuestionIndex.value])

const vote = (index) => {
  selectedOption.value = index
  hasVoted.value = true
  if (index === currentQuestion.value.correctAnswer) {
    score.value++
  }
}

const nextQuestion = () => {
  slideLeft.value = true
  setTimeout(() => {
    hasVoted.value = false
    selectedOption.value = null
    currentQuestionIndex.value++
    if (currentQuestionIndex.value >= questions.length) {
      quizComplete.value = true
    }
    slideLeft.value = false
  }, 300)
}

const previousQuestion = () => {
  slideRight.value = true
  setTimeout(() => {
    hasVoted.value = false
    selectedOption.value = null
    currentQuestionIndex.value--
    slideRight.value = false
  }, 300)
}

const restartQuiz = () => {
  currentQuestionIndex.value = 0
  hasVoted.value = false
  selectedOption.value = null
  score.value = 0
  quizComplete.value = false
}
</script>

<style scoped>
.poll-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 2rem;
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  position: relative;
  transition: transform 0.3s ease-in-out;
}

.poll-container.slide-left {
  transform: translateX(-100%);
  opacity: 0;
}

.poll-container.slide-right {
  transform: translateX(100%);
  opacity: 0;
}

.poll-question {
  margin-bottom: 1.5rem;
  color: #2c3e50;
  font-size: 1.5rem;
  text-align: center;
}

.progress {
  text-align: center;
  color: #666;
  margin-bottom: 1rem;
  font-size: 0.9rem;
}

.score-display {
  text-align: center;
  color: #2c3e50;
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 1rem;
  padding: 1rem;
  background: #f8f9fa;
  border-radius: 8px;
}

.options-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.option {
  padding: 1rem;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.option:hover:not(.voted) {
  border-color: #3498db;
  transform: translateY(-2px);
}

.option.voted {
  cursor: default;
}

.option.correct {
  border-color: #2ecc71;
  background-color: rgba(46, 204, 113, 0.1);
}

.option.incorrect {
  border-color: #e74c3c;
  background-color: rgba(231, 76, 60, 0.1);
}

.option-content {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.option-text {
  color: #2c3e50;
  font-weight: 500;
}

.result-indicator {
  font-weight: bold;
  font-size: 1.2rem;
}

.navigation-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 2rem;
  gap: 1rem;
}

.nav-button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 6px;
  background-color: #3498db;
  color: white;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 500;
}

.nav-button:hover {
  background-color: #2980b9;
  transform: translateY(-2px);
}

.nav-button.restart {
  background-color: #2ecc71;
}

.nav-button.restart:hover {
  background-color: #27ae60;
}

/* Fade transition */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
