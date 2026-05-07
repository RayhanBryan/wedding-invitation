<script setup>
import { ref, computed, watch } from 'vue'

const emit = defineEmits(['win', 'back'])

const EMOJIS = ['💍', '💎', '🌸', '🎮', '🕹️', '💌', '🌹', '👑']
const allCards = [...EMOJIS, ...EMOJIS]

function shuffle(arr) {
  const a = [...arr]
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[a[i], a[j]] = [a[j], a[i]]
  }
  return a
}

const cards = ref([])
const flipped = ref([]) // indices currently face-up (max 2)
const matched = ref([]) // indices that are matched
const moves = ref(0)
const gameState = ref('idle') // idle | playing | win
let lockBoard = false

function startGame() {
  cards.value = shuffle(allCards).map((e, i) => ({ id: i, emoji: e }))
  flipped.value = []
  matched.value = []
  moves.value = 0
  lockBoard = false
  gameState.value = 'playing'
}

function isFlipped(i) {
  return flipped.value.includes(i) || matched.value.includes(i)
}
function isMatched(i) {
  return matched.value.includes(i)
}

function flip(i) {
  if (lockBoard) return
  if (isFlipped(i)) return
  if (flipped.value.length === 2) return

  flipped.value = [...flipped.value, i]

  if (flipped.value.length === 2) {
    moves.value++
    lockBoard = true
    const [a, b] = flipped.value
    if (cards.value[a].emoji === cards.value[b].emoji) {
      matched.value = [...matched.value, a, b]
      flipped.value = []
      lockBoard = false
      if (matched.value.length === cards.value.length) {
        setTimeout(() => {
          gameState.value = 'win'
        }, 500)
      }
    } else {
      setTimeout(() => {
        flipped.value = []
        lockBoard = false
      }, 900)
    }
  }
}
</script>

<template>
  <div class="game-screen memory-screen">
    <div class="game-header">
      <h2>🃏 MEMORY</h2>
      <div class="score-display">MOVES: {{ moves }}</div>
      <button
        class="pixel-btn secondary"
        style="font-size: 0.45rem; padding: 0.5rem 0.8rem"
        @click="$emit('back')"
      >
        ✕ BACK
      </button>
    </div>

    <!-- IDLE -->
    <div v-if="gameState === 'idle'" class="memory-overlay">
      <div class="overlay-content">
        <p class="gold-text">🃏 MEMORY</p>
        <p class="small-text">Cocokkan semua pasangan<br />kartu untuk menang!</p>
        <p class="small-text" style="color: #666">16 kartu • 8 pasang</p>
        <button class="pixel-btn pink" @click="startGame">▶ START</button>
      </div>
    </div>

    <!-- PLAYING -->
    <div v-else-if="gameState === 'playing'" class="card-grid">
      <button
        v-for="(card, i) in cards"
        :key="card.id"
        class="mem-card"
        :class="{
          flipped: isFlipped(i),
          matched: isMatched(i),
        }"
        @click="flip(i)"
      >
        <span class="card-front">{{ card.emoji }}</span>
        <span class="card-back">?</span>
      </button>
    </div>

    <!-- WIN -->
    <div v-else-if="gameState === 'win'" class="memory-overlay win-overlay">
      <div class="overlay-content">
        <p class="green-text">🎉 CLEAR!</p>
        <p class="small-text gold-text">Selesai dalam {{ moves }} moves!</p>
        <p class="small-text">Undangan siap dibuka!</p>
        <button class="pixel-btn gold" @click="$emit('win')">💍 BUKA UNDANGAN</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.memory-screen {
  justify-content: flex-start;
  padding-top: 1rem;
}

.card-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  width: 100%;
  max-width: 400px;
}

.mem-card {
  font-family: 'Press Start 2P', monospace;
  aspect-ratio: 1;
  background: #1a0a3e;
  border: 3px solid #ff69b4;
  box-shadow:
    3px 3px 0 #880044,
    5px 5px 0 #000;
  cursor: pointer;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: clamp(1.2rem, 5vw, 2rem);
  transition: transform 0.1s;
  overflow: hidden;
}
.mem-card:active {
  transform: translate(2px, 2px);
}

.card-front {
  display: none;
}
.card-back {
  color: #ff69b4;
  font-size: clamp(0.6rem, 3vw, 1rem);
  text-shadow: 2px 2px 0 #000;
}

.mem-card.flipped .card-front {
  display: block;
}
.mem-card.flipped .card-back {
  display: none;
}
.mem-card.flipped {
  background: #0d1a0d;
  border-color: #00ff41;
  box-shadow:
    3px 3px 0 #007722,
    5px 5px 0 #000;
}

.mem-card.matched .card-front {
  display: block;
}
.mem-card.matched .card-back {
  display: none;
}
.mem-card.matched {
  background: #0a200a;
  border-color: #ffdd00;
  box-shadow:
    3px 3px 0 #886600,
    5px 5px 0 #000;
  cursor: default;
  animation: matchPop 0.3s ease;
}

@keyframes matchPop {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.15);
  }
  100% {
    transform: scale(1);
  }
}

.memory-overlay {
  width: 100%;
  max-width: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 400px;
  background: rgba(0, 0, 0, 0.85);
  border: 4px solid #ff69b4;
  box-shadow: 8px 8px 0 #000;
}
.win-overlay {
  border-color: #ffdd00;
}
</style>
