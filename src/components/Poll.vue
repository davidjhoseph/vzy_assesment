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
  max-width: 800px;
  margin: 2rem auto;
  padding: 2.5rem;
  background: linear-gradient(145deg, #ffffff, #f0f0f0);
  border-radius: 20px;
  box-shadow: 
    0 10px 20px rgba(0, 0, 0, 0.1),
    0 6px 6px rgba(0, 0, 0, 0.06);
  position: relative;
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.poll-container.slide-left {
  transform: translateX(-100%) scale(0.9);
  opacity: 0;
}

.poll-container.slide-right {
  transform: translateX(100%) scale(0.9);
  opacity: 0;
}

.poll-question {
  margin-bottom: 2rem;
  color: #1a1a1a;
  font-size: 1.8rem;
  text-align: center;
  font-weight: 600;
  line-height: 1.4;
  text-shadow: 1px 1px 1px rgba(255, 255, 255, 0.5);
}

.progress {
  text-align: center;
  color: #666;
  margin-bottom: 1.5rem;
  font-size: 1rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 500;
}

.score-display {
  text-align: center;
  color: #2c3e50;
  font-size: 2rem;
  font-weight: 700;
  margin: 2rem 0;
  padding: 2rem;
  background: linear-gradient(135deg, #6366f1 0%, #4f46e5 100%);
  color: white;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(79, 70, 229, 0.3);
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.options-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  margin: 2rem 0;
}

.option {
  padding: 1.25rem;
  border: 2px solid #e2e8f0;
  border-radius: 16px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
  background: white;
}

.option:hover:not(.voted) {
  border-color: #6366f1;
  transform: translateY(-4px);
  box-shadow: 0 8px 16px rgba(99, 102, 241, 0.15);
}

.option.voted {
  cursor: default;
}

.option.correct {
  border-color: #10b981;
  background: linear-gradient(145deg, #ecfdf5, #d1fae5);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.2);
}

.option.incorrect {
  border-color: #ef4444;
  background: linear-gradient(145deg, #fef2f2, #fee2e2);
  box-shadow: 0 4px 12px rgba(239, 68, 68, 0.2);
}

.option-content {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
}

.option-text {
  color: #1f2937;
  font-weight: 500;
  font-size: 1.1rem;
  flex-grow: 1;
}

.result-indicator {
  font-weight: bold;
  font-size: 1.4rem;
  opacity: 0;
  animation: fadeIn 0.5s forwards;
}

.navigation-buttons {
  display: flex;
  justify-content: center;
  margin-top: 2.5rem;
  gap: 1.5rem;
}

.nav-button {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 12px;
  background: linear-gradient(135deg, #6366f1 0%, #4f46e5 100%);
  color: white;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 600;
  font-size: 1rem;
  letter-spacing: 0.5px;
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
}

.nav-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(99, 102, 241, 0.4);
}

.nav-button.restart {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}

.nav-button.restart:hover {
  box-shadow: 0 6px 16px rgba(16, 185, 129, 0.4);
}

/* Animations */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.fade-enter-active {
  animation: fadeIn 0.5s ease-out;
}

.fade-leave-active {
  animation: fadeIn 0.5s ease-in reverse;
}

/* Responsive Design */
@media (max-width: 768px) {
  .poll-container {
    margin: 1rem;
    padding: 1.5rem;
  }

  .poll-question {
    font-size: 1.5rem;
  }

  .options-container {
    grid-template-columns: 1fr;
  }

  .navigation-buttons {
    flex-direction: column;
    align-items: stretch;
  }

  .nav-button {
    width: 100%;
  }
}
</style>
