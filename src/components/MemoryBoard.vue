<template>
  <div>
    <div v-if="gameOver" class="game-over">
      <h2>🎉 Поздравляем! 🎉</h2>
      <p>Вы завершили игру за <strong>{{ moves }}</strong> ходов!</p>
      <button @click="resetGame">Играть снова</button>
    </div>
    <div v-else class="board" :style="boardStyle">
      <MemoryCard
        v-for="(card, index) in cards"
        :key="card.id"
        :card="card"
        :flipped="isFlipped(card)"
        :matched="card.matched"
        :style="{ animationDelay: `${index * 0.1}s` }"
        @click="handleCardClick(card)"
      />
      <div v-if="showParticles" class="particles">
        <div v-for="i in 12" :key="i" class="particle" :style="{ '--i': i }"></div>
      </div>
    </div>
    <div class="moves" :class="{ 'moves-updated': moves > 0 }">Ходы: {{ moves }}</div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import MemoryCard from './MemoryCard.vue'

const N = 4
const delay = 500

const icons = [
  '🍎','🍌','🍇','🍓',
  '🍑','🍍','🥝','🍒'
]

const moves = ref(0)
const selectedCards = ref([])
const lockBoard = ref(false)
const gameOver = ref(false)
const showParticles = ref(false)

const cards = ref([])

function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[array[i], array[j]] = [array[j], array[i]]
  }
  return array
}

function initCards() {
  const pairIcons = icons.slice(0, (N * N) / 2)
  const allIcons = shuffle([...pairIcons, ...pairIcons])
  cards.value = allIcons.map((icon, index) => ({
    id: index,
    icon,
    matched: false
  }))
  moves.value = 0
  selectedCards.value = []
  gameOver.value = false
  lockBoard.value = false
  showParticles.value = false
}

function isFlipped(card) {
  return selectedCards.value.includes(card) || card.matched
}

function handleCardClick(card) {
  if (lockBoard.value) return
  if (card.matched) return
  if (selectedCards.value.includes(card)) return

  selectedCards.value.push(card)

  if (selectedCards.value.length === 2) {
    moves.value++
    lockBoard.value = true
    const [first, second] = selectedCards.value

    if (first.icon === second.icon) {
      first.matched = true
      second.matched = true
      selectedCards.value = []
      lockBoard.value = false
      
      // Показываем эффект частиц при совпадении
      showParticles.value = true
      setTimeout(() => {
        showParticles.value = false
      }, 1000)
      
      if (cards.value.every(c => c.matched)) {
        setTimeout(() => {
          gameOver.value = true
        }, 500)
      }
    } else {
      setTimeout(() => {
        selectedCards.value = []
        lockBoard.value = false
      }, delay)
    }
  }
}

function resetGame() {
  initCards()
}

initCards()

const boardStyle = computed(() => ({
  display: 'grid',
  gridTemplateColumns: `repeat(${N}, 100px)`,
  gridTemplateRows: `repeat(${N}, 100px)`,
  gap: '10px',
  justifyContent: 'center'
}))
</script>

<style scoped>
.board {
  margin: 20px auto;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  backdrop-filter: blur(10px);
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.1);
  animation: slideInUp 0.8s ease-out;
  position: relative;
}

.moves {
  margin-top: 20px;
  padding: 15px 25px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 25px;
  backdrop-filter: blur(5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  animation: fadeInUp 0.8s ease-out 0.3s both;
  transition: all 0.3s ease;
}

.moves-updated {
  animation: movesPulse 0.6s ease-out;
}

.game-over {
  text-align: center;
  padding: 40px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  animation: zoomIn 0.6s ease-out;
}

.game-over h2 {
  color: #333;
  font-size: 2.5rem;
  margin-bottom: 20px;
  background: linear-gradient(45deg, #667eea, #764ba2);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.game-over p {
  color: #666;
  font-size: 1.2rem;
  margin-bottom: 30px;
}

.game-over button {
  background: linear-gradient(45deg, #4CAF50, #45a049);
  padding: 15px 30px;
  font-size: 18px;
  border-radius: 30px;
  border: none;
  color: white;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 8px 25px rgba(76, 175, 80, 0.3);
}

.game-over button:hover {
  transform: translateY(-3px);
  box-shadow: 0 12px 30px rgba(76, 175, 80, 0.4);
  background: linear-gradient(45deg, #45a049, #4CAF50);
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes zoomIn {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.particles {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.particle {
  position: absolute;
  width: 8px;
  height: 8px;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4);
  border-radius: 50%;
  animation: particleExplosion 1s ease-out forwards;
  animation-delay: calc(var(--i) * 0.1s);
}

@keyframes particleExplosion {
  0% {
    opacity: 1;
    transform: translate(0, 0) scale(1);
  }
  100% {
    opacity: 0;
    transform: translate(
      calc(cos(var(--i) * 30deg) * 100px),
      calc(sin(var(--i) * 30deg) * 100px)
    ) scale(0);
  }
}

@keyframes movesPulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
    background: rgba(255, 255, 255, 0.3);
  }
  100% {
    transform: scale(1);
  }
}
</style>
