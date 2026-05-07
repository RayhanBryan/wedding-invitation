<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const emit = defineEmits(['win', 'back'])

const canvas = ref(null)
const W = 320,
  H = 400
const WIN_SCORE = 15
const BASKET_W = 52,
  BASKET_H = 24
const BASKET_SPEED = 6

let ctx, rafId, gameRunning
let basket, hearts, particles, frameCount, missed

const score = ref(0)
const lives = ref(3)
const gameState = ref('idle')

function resetState() {
  basket = { x: W / 2 - BASKET_W / 2, y: H - 40 }
  hearts = []
  particles = []
  frameCount = 0
  missed = 0
  score.value = 0
  lives.value = 3
}

// ── Controls ──
const keys = {}
function onKey(e) {
  keys[e.code] = e.type === 'keydown'
  if (['ArrowLeft', 'ArrowRight', 'KeyA', 'KeyD'].includes(e.code)) e.preventDefault()
}

function dpad(dir) {
  if (dir === 'left') basket.x = Math.max(0, basket.x - BASKET_SPEED * 5)
  if (dir === 'right') basket.x = Math.min(W - BASKET_W, basket.x + BASKET_SPEED * 5)
}

let touchStartX = 0,
  basketStartX = 0
function onTouchStart(e) {
  touchStartX = e.touches[0].clientX
  basketStartX = basket.x
}
function onTouchMove(e) {
  e.preventDefault()
  const rect = canvas.value.getBoundingClientRect()
  const scaleX = W / rect.width
  basket.x = Math.max(
    0,
    Math.min(W - BASKET_W, basketStartX + (e.touches[0].clientX - touchStartX) * scaleX),
  )
}

// ── Spawn ──
function spawnHeart() {
  const types = [
    { color: '#ff0044', pts: 1, speed: 1.8 + Math.random() * 1.2, size: 5 },
    { color: '#ff69b4', pts: 2, speed: 2.2 + Math.random() * 1.5, size: 4 },
    { color: '#ffdd00', pts: 3, speed: 2.8 + Math.random() * 2, size: 3 },
  ]
  const t = types[Math.floor(Math.random() * types.length)]
  hearts.push({
    x: 16 + Math.random() * (W - 32),
    y: -20,
    vy: t.speed,
    color: t.color,
    pts: t.pts,
    size: t.size,
  })
}

function spawnParticles(x, y, color) {
  for (let i = 0; i < 8; i++) {
    const angle = (i / 8) * Math.PI * 2
    particles.push({
      x,
      y,
      vx: Math.cos(angle) * (1.5 + Math.random() * 2),
      vy: Math.sin(angle) * (1.5 + Math.random() * 2),
      color,
      life: 25,
    })
  }
}

// ── Draw helpers ──
function drawPixelHeart(x, y, color, size = 4) {
  const s = size
  const pattern = [
    [0, 1, 1, 0, 1, 1, 0],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [0, 1, 1, 1, 1, 1, 0],
    [0, 0, 1, 1, 1, 0, 0],
    [0, 0, 0, 1, 0, 0, 0],
  ]
  ctx.fillStyle = color
  pattern.forEach((row, ry) =>
    row.forEach((p, rx) => {
      if (p) ctx.fillRect(Math.floor(x - 3.5 * s + rx * s), Math.floor(y - 3 * s + ry * s), s, s)
    }),
  )
}

function drawBasket() {
  const bx = Math.floor(basket.x),
    by = Math.floor(basket.y)
  ctx.fillStyle = '#884400'
  ctx.fillRect(bx, by + 8, BASKET_W, BASKET_H - 8)
  ctx.fillStyle = '#aa6622'
  for (let i = 0; i < 3; i++) ctx.fillRect(bx + 4 + i * 14, by + 10, 8, BASKET_H - 12)
  ctx.fillStyle = '#cc8833'
  ctx.fillRect(bx - 4, by + 4, BASKET_W + 8, 8)
  ctx.fillStyle = '#ffaa44'
  ctx.fillRect(bx, by + 4, 6, 4)
  drawPixelHeart(bx + BASKET_W / 2, by + 18, '#ff0044', 2)
}

function drawBg() {
  ctx.fillStyle = '#0a0a2a'
  ctx.fillRect(0, 0, W, H)
  for (let i = 0; i < 25; i++) {
    ctx.globalAlpha = 0.3 + (i % 3) * 0.2
    ctx.fillStyle = '#fff'
    ctx.fillRect(
      Math.floor((i * 83 + frameCount * 0.1) % W),
      Math.floor((i * 57) % (H * 0.7)),
      2,
      2,
    )
  }
  ctx.globalAlpha = 1
  ctx.fillStyle = '#1a0a0a'
  ctx.fillRect(0, H - 16, W, 16)
  ctx.fillStyle = '#2a1010'
  for (let gx = ((frameCount * 1) % 32) - 32; gx < W; gx += 32) ctx.fillRect(gx, H - 16, 16, 6)
  ctx.fillStyle = '#1a1a3a'
  ctx.fillRect(0, 0, W, 28)
  ctx.fillStyle = '#2a2a5a'
  ctx.fillRect(0, 26, W, 2)
}

function drawHUD() {
  ctx.fillStyle = '#ffdd00'
  ctx.font = '8px "Press Start 2P", monospace'
  ctx.textAlign = 'left'
  ctx.fillText(`SCORE:${score.value}`, 6, 18)
  for (let i = 0; i < 3; i++)
    drawPixelHeart(W - 45 + i * 16, 14, i < lives.value ? '#ff0044' : '#330011', 2)
}

// ── Main loop ──
function loop() {
  if (!gameRunning || !canvas.value) return
  ctx.clearRect(0, 0, W, H)
  frameCount++
  drawBg()
  const spawnRate = Math.max(30, 70 - Math.floor(score.value * 2))
  if (frameCount % spawnRate === 0) spawnHeart()
  hearts = hearts.filter((h) => {
    h.y += h.vy
    drawPixelHeart(h.x, h.y, h.color, h.size)
    if (
      h.y + 12 >= basket.y + 4 &&
      h.y - 12 <= basket.y + BASKET_H &&
      h.x >= basket.x - 8 &&
      h.x <= basket.x + BASKET_W + 8
    ) {
      score.value += h.pts
      spawnParticles(h.x, h.y, h.color)
      if (score.value >= WIN_SCORE) {
        winGame()
        return false
      }
      return false
    }
    if (h.y > H + 10) {
      missed++
      lives.value = Math.max(0, 3 - missed)
      if (missed >= 3) {
        endGame()
        return false
      }
      return false
    }
    return true
  })
  particles = particles.filter((p) => {
    p.x += p.vx
    p.y += p.vy
    p.life--
    ctx.globalAlpha = p.life / 25
    ctx.fillStyle = p.color
    ctx.fillRect(Math.floor(p.x), Math.floor(p.y), 4, 4)
    ctx.globalAlpha = 1
    return p.life > 0
  })
  if (keys['ArrowLeft'] || keys['KeyA']) basket.x = Math.max(0, basket.x - BASKET_SPEED)
  if (keys['ArrowRight'] || keys['KeyD']) basket.x = Math.min(W - BASKET_W, basket.x + BASKET_SPEED)
  drawBasket()
  drawHUD()
  rafId = requestAnimationFrame(loop)
}

function startGame() {
  cancelAnimationFrame(rafId)
  resetState()
  gameState.value = 'playing'
  gameRunning = true
  loop()
}
function endGame() {
  gameRunning = false
  gameState.value = 'over'
}
function winGame() {
  gameRunning = false
  gameState.value = 'win'
}

onMounted(() => {
  ctx = canvas.value.getContext('2d')
  ctx.fillStyle = '#0a0a2a'
  ctx.fillRect(0, 0, W, H)
  window.addEventListener('keydown', onKey)
  window.addEventListener('keyup', onKey)
  canvas.value.addEventListener('touchstart', onTouchStart, { passive: true })
  canvas.value.addEventListener('touchmove', onTouchMove, { passive: false })
})
onUnmounted(() => {
  gameRunning = false
  cancelAnimationFrame(rafId)
  window.removeEventListener('keydown', onKey)
  window.removeEventListener('keyup', onKey)
})
</script>

<template>
  <div class="game-screen">
    <div class="game-header">
      <h2>🧺 CATCH ♥</h2>
      <div class="score-display">{{ score }}/{{ WIN_SCORE }}</div>
      <button
        class="pixel-btn secondary"
        style="font-size: 0.45rem; padding: 0.5rem 0.8rem"
        @click="$emit('back')"
      >
        ✕ BACK
      </button>
    </div>

    <div class="canvas-wrapper" style="max-width: 320px">
      <canvas ref="canvas" :width="W" :height="H"></canvas>

      <!-- IDLE -->
      <div v-if="gameState === 'idle'" class="canvas-overlay">
        <div class="overlay-content">
          <p class="gold-text">🧺 CATCH THE HEARTS</p>
          <p class="small-text">
            Tangkap {{ WIN_SCORE }} hati<br />yang jatuh dari langit!<br /><br />
            <span style="color: #ff0044">♥</span> = 1pt &nbsp;
            <span style="color: #ff69b4">♥</span> = 2pt &nbsp;
            <span style="color: #ffdd00">♥</span> = 3pt<br /><br />
            3 hati terlewat = game over!
          </p>
          <button class="pixel-btn green" @click="startGame">▶ START</button>
        </div>
      </div>

      <!-- GAME OVER -->
      <div v-if="gameState === 'over'" class="canvas-overlay">
        <div class="overlay-content">
          <p class="red-text">GAME OVER!</p>
          <p class="small-text">Score: {{ score }}/{{ WIN_SCORE }}</p>
          <button class="pixel-btn gold" @click="startGame">↺ RETRY</button>
        </div>
      </div>

      <!-- WIN -->
      <div v-if="gameState === 'win'" class="canvas-overlay win-overlay">
        <div class="overlay-content">
          <p class="green-text">🎉 CLEAR!</p>
          <p class="small-text gold-text">{{ score }} HEARTS CAUGHT!</p>
          <p class="small-text">Undangan siap dibuka!</p>
          <button class="pixel-btn gold" @click="$emit('win')">💍 BUKA UNDANGAN</button>
        </div>
      </div>
    </div>

    <!-- Controls -->
    <div class="mobile-controls">
      <div style="display: flex; gap: 8px; align-items: center">
        <button
          class="dpad-btn"
          style="width: 72px; height: 52px; font-size: 1.2rem"
          @click="dpad('left')"
        >
          ◀
        </button>
        <div
          style="
            width: 40px;
            height: 52px;
            background: #111;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.4rem;
            color: #444;
            text-align: center;
          "
        >
          MOVE
        </div>
        <button
          class="dpad-btn"
          style="width: 72px; height: 52px; font-size: 1.2rem"
          @click="dpad('right')"
        >
          ▶
        </button>
      </div>
    </div>
    <p style="font-size: 0.4rem; color: #444; margin-top: 0.25rem">
      ← → / A D untuk gerak • Drag layar di mobile
    </p>
  </div>
</template>
