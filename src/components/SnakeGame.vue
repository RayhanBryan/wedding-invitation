<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const emit = defineEmits(['win', 'back'])

const canvas = ref(null)
const GRID = 20
const CELL = 20
const W = GRID * CELL
const H = GRID * CELL
const WIN_SCORE = 10

let ctx, gameInterval
let snake = [],
  food = {},
  dir = { x: 1, y: 0 },
  nextDir = { x: 1, y: 0 }

const score = ref(0)
const gameState = ref('idle') // idle | playing | over | win

function rnd(n) {
  return Math.floor(Math.random() * n)
}

function spawnFood() {
  let pos
  do {
    pos = { x: rnd(GRID), y: rnd(GRID) }
  } while (snake.some((s) => s.x === pos.x && s.y === pos.y))
  return pos
}

function init() {
  snake = [
    { x: 12, y: 10 },
    { x: 11, y: 10 },
    { x: 10, y: 10 },
  ]
  dir = { x: 1, y: 0 }
  nextDir = { x: 1, y: 0 }
  food = spawnFood()
  score.value = 0
  gameState.value = 'playing'
}

function draw() {
  if (!ctx) return
  ctx.fillStyle = '#060610'
  ctx.fillRect(0, 0, W, H)
  // Grid dots
  ctx.fillStyle = '#0d0d22'
  for (let x = 0; x < GRID; x++)
    for (let y = 0; y < GRID; y++) {
      ctx.fillRect(x * CELL + CELL / 2 - 1, y * CELL + CELL / 2 - 1, 2, 2)
    }
  // Food (blinking pixel heart)
  ctx.fillStyle = '#ff0044'
  const fx = food.x * CELL,
    fy = food.y * CELL
  const heart = [
    [0, 1, 0, 1, 0],
    [1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1],
    [0, 1, 1, 1, 0],
    [0, 0, 1, 0, 0],
  ]
  heart.forEach((row, ry) =>
    row.forEach((px, rx) => {
      if (px) ctx.fillRect(fx + rx * 4, fy + ry * 4, 4, 4)
    }),
  )
  // Snake
  snake.forEach((seg, i) => {
    ctx.fillStyle = i === 0 ? '#00ff41' : i % 2 === 0 ? '#00cc33' : '#009922'
    ctx.fillRect(seg.x * CELL + 1, seg.y * CELL + 1, CELL - 2, CELL - 2)
    if (i === 0) {
      ctx.fillStyle = '#000'
      const ex = dir.x === 1 ? CELL - 6 : 2,
        ey = dir.y === 1 ? CELL - 6 : 2
      const eyeOffX = dir.y !== 0 ? 4 : 0
      ctx.fillRect(seg.x * CELL + ex - eyeOffX, seg.y * CELL + ey, 3, 3)
      ctx.fillRect(seg.x * CELL + ex + eyeOffX + 2, seg.y * CELL + ey, 3, 3)
    }
  })
}

function tick() {
  if (gameState.value !== 'playing') return
  dir = { ...nextDir }
  const head = { x: snake[0].x + dir.x, y: snake[0].y + dir.y }
  // Wall collision
  if (head.x < 0 || head.x >= GRID || head.y < 0 || head.y >= GRID) {
    endGame()
    return
  }
  // Self collision
  if (snake.some((s) => s.x === head.x && s.y === head.y)) {
    endGame()
    return
  }
  snake.unshift(head)
  if (head.x === food.x && head.y === food.y) {
    score.value++
    if (score.value >= WIN_SCORE) {
      draw()
      winGame()
      return
    }
    food = spawnFood()
  } else {
    snake.pop()
  }
  draw()
}

function endGame() {
  gameState.value = 'over'
  clearInterval(gameInterval)
  draw()
}
function winGame() {
  gameState.value = 'win'
  clearInterval(gameInterval)
}

function startGame() {
  clearInterval(gameInterval)
  init()
  draw()
  gameInterval = setInterval(tick, 150)
}

function onKey(e) {
  const map = {
    ArrowUp: { x: 0, y: -1 },
    ArrowDown: { x: 0, y: 1 },
    ArrowLeft: { x: -1, y: 0 },
    ArrowRight: { x: 1, y: 0 },
    w: { x: 0, y: -1 },
    s: { x: 0, y: 1 },
    a: { x: -1, y: 0 },
    d: { x: 1, y: 0 },
  }
  if (map[e.key]) {
    const nd = map[e.key]
    if (nd.x !== -dir.x || nd.y !== -dir.y) nextDir = nd
    e.preventDefault()
  }
}

function dpad(dx, dy) {
  const nd = { x: dx, y: dy }
  if (nd.x !== -dir.x || nd.y !== -dir.y) nextDir = nd
}

onMounted(() => {
  ctx = canvas.value.getContext('2d')
  draw()
  window.addEventListener('keydown', onKey)
})
onUnmounted(() => {
  clearInterval(gameInterval)
  window.removeEventListener('keydown', onKey)
})
</script>

<template>
  <div class="game-screen">
    <div class="game-header">
      <h2>🐍 SNAKE</h2>
      <div class="score-display">SCORE: {{ score }}/{{ 10 }}</div>
      <button
        class="pixel-btn secondary"
        style="font-size: 0.45rem; padding: 0.5rem 0.8rem"
        @click="$emit('back')"
      >
        ✕ BACK
      </button>
    </div>

    <div class="canvas-wrapper">
      <canvas ref="canvas" :width="W" :height="H"></canvas>

      <!-- IDLE -->
      <div v-if="gameState === 'idle'" class="canvas-overlay">
        <div class="overlay-content">
          <p class="gold-text">🐍 SNAKE</p>
          <p class="small-text">Makan {{ 10 }} makanan ♥<br />untuk menang!</p>
          <button class="pixel-btn green" @click="startGame">▶ START</button>
        </div>
      </div>

      <!-- GAME OVER -->
      <div v-if="gameState === 'over'" class="canvas-overlay">
        <div class="overlay-content">
          <p class="red-text">GAME OVER!</p>
          <p class="small-text">Score: {{ score }}</p>
          <button class="pixel-btn green" @click="startGame">↺ RETRY</button>
        </div>
      </div>

      <!-- WIN -->
      <div v-if="gameState === 'win'" class="canvas-overlay win-overlay">
        <div class="overlay-content">
          <p class="green-text">🎉 CLEAR!</p>
          <p class="small-text gold-text">Score: {{ score }}/{{ 10 }}</p>
          <p class="small-text">Undangan siap dibuka!</p>
          <button class="pixel-btn gold" @click="$emit('win')">💍 BUKA UNDANGAN</button>
        </div>
      </div>
    </div>

    <!-- Mobile D-PAD -->
    <div class="mobile-controls">
      <div class="dpad">
        <div class="dpad-row"><button class="dpad-btn" @click="dpad(0, -1)">▲</button></div>
        <div class="dpad-row">
          <button class="dpad-btn" @click="dpad(-1, 0)">◀</button>
          <div class="dpad-center"></div>
          <button class="dpad-btn" @click="dpad(1, 0)">▶</button>
        </div>
        <div class="dpad-row"><button class="dpad-btn" @click="dpad(0, 1)">▼</button></div>
      </div>
    </div>

    <p class="hint-text">Keyboard: WASD / Arrow Keys</p>
  </div>
</template>

<style scoped>
.hint-text {
  font-size: 0.4rem;
  color: #444;
  margin-top: 0.5rem;
}
</style>
