<script setup>
import { ref } from 'vue'
import GameSelector from './components/GameSelector.vue'
import SnakeGame from './components/SnakeGame.vue'
import MemoryGame from './components/MemoryGame.vue'
import FlappyGame from './components/FlappyGame.vue'
import WeddingInvitation from './components/WeddingInvitation.vue'

const phase = ref('intro')
const activeGame = ref('')

function onStart() {
  phase.value = 'select'
}
function onSelectGame(g) {
  activeGame.value = g
  phase.value = 'playing'
}
function onWin() {
  phase.value = 'invitation'
}
function onBack() {
  activeGame.value = ''
  phase.value = 'select'
}
</script>

<template>
  <div class="app">
    <div class="scanlines" aria-hidden="true"></div>

    <!-- INTRO -->
    <div v-if="phase === 'intro'" class="screen intro-screen">
      <div class="pixel-title">
        <div class="blink top-label">◀ INSERT COIN ▶</div>
        <h1 class="main-title">WEDDING<br />INVITATION</h1>
        <div class="heart-row">♥ ♥ ♥ ♥ ♥</div>
        <p class="subtitle">⚔ UNLOCK TO REVEAL ⚔</p>
        <p class="hint">Selesaikan salah satu game<br />untuk membuka undangan kami</p>
        <button class="pixel-btn gold" @click="onStart">▶ PRESS START</button>
        <div class="copyright">© 2026 PIXEL WEDDING STUDIO</div>
      </div>
    </div>

    <!-- SELECT -->
    <GameSelector v-else-if="phase === 'select'" @select="onSelectGame" />

    <!-- GAMES -->
    <template v-else-if="phase === 'playing'">
      <SnakeGame v-if="activeGame === 'snake'" @win="onWin" @back="onBack" />
      <MemoryGame v-if="activeGame === 'memory'" @win="onWin" @back="onBack" />
      <FlappyGame v-if="activeGame === 'flappy'" @win="onWin" @back="onBack" />
    </template>

    <!-- INVITATION -->
    <WeddingInvitation v-else-if="phase === 'invitation'" />
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

:root {
  --gold: #ffdd00;
  --orange: #ff6600;
  --green: #00ff41;
  --pink: #ff69b4;
  --red: #ff0044;
  --blue: #00aaff;
  --dark: #0a0a1a;
  --darker: #050510;
}

html {
  scroll-behavior: smooth;
}
body {
  background: var(--darker);
  font-family: 'Press Start 2P', monospace;
  color: #fff;
  overflow-x: hidden;
}
.app {
  min-height: 100vh;
  position: relative;
}

.scanlines {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9999;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 0, 0, 0.13) 2px,
    rgba(0, 0, 0, 0.13) 4px
  );
}

.screen {
  min-height: 100vh;
  min-height: 100dvh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem 1rem;
}

.intro-screen {
  background: radial-gradient(ellipse at 50% 30%, #2a0a4e 0%, #0a0a1a 65%);
  align-items: flex-start;
  padding-top: max(2rem, env(safe-area-inset-top));
  padding-bottom: max(2rem, env(safe-area-inset-bottom));
}

.pixel-title {
  text-align: center;
  max-width: 560px;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.6rem;
  padding: 1rem 0;
}

.top-label {
  font-size: clamp(0.45rem, 2vw, 0.6rem);
  color: var(--green);
  letter-spacing: 0.1em;
  margin-bottom: 1.5rem;
}

.main-title {
  font-size: clamp(2rem, 10vw, 4.5rem);
  color: var(--gold);
  text-shadow:
    4px 4px 0 var(--orange),
    8px 8px 0 #000;
  line-height: 1.3;
  margin-bottom: 1rem;
}

.heart-row {
  color: var(--pink);
  font-size: 1.2rem;
  letter-spacing: 0.5rem;
  margin: 0.5rem 0;
  animation: heartbeat 1.5s ease-in-out infinite;
}
@keyframes heartbeat {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

.subtitle {
  color: var(--orange);
  font-size: clamp(0.5rem, 2.5vw, 0.75rem);
  margin: 0.5rem 0 0.25rem;
}
.hint {
  color: #888;
  font-size: clamp(0.4rem, 2vw, 0.55rem);
  line-height: 2.2;
  margin: 0.75rem 0 1rem;
}
.copyright {
  margin-top: 1rem;
  font-size: 0.35rem;
  color: #444;
}

.blink {
  animation: blink 1s step-end infinite;
}
@keyframes blink {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
}

/* ---- BUTTONS ---- */
.pixel-btn {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.55rem, 2.5vw, 0.8rem);
  padding: 0.9rem 1.8rem;
  border: none;
  cursor: pointer;
  position: relative;
  transition:
    transform 0.08s,
    box-shadow 0.08s;
  line-height: 1;
}
.pixel-btn.gold {
  color: #000;
  background: var(--gold);
  box-shadow:
    4px 4px 0 var(--orange),
    8px 8px 0 #000;
}
.pixel-btn.green {
  color: #000;
  background: var(--green);
  box-shadow:
    4px 4px 0 #007722,
    8px 8px 0 #000;
}
.pixel-btn.pink {
  color: #000;
  background: var(--pink);
  box-shadow:
    4px 4px 0 #cc3377,
    8px 8px 0 #000;
}
.pixel-btn.red {
  color: #fff;
  background: var(--red);
  box-shadow:
    4px 4px 0 #880022,
    8px 8px 0 #000;
}
.pixel-btn.secondary {
  color: #aaa;
  background: #222;
  box-shadow:
    4px 4px 0 #111,
    8px 8px 0 #000;
}

.pixel-btn.gold:hover {
  transform: translate(2px, 2px);
  box-shadow:
    2px 2px 0 var(--orange),
    4px 4px 0 #000;
}
.pixel-btn.green:hover {
  transform: translate(2px, 2px);
  box-shadow:
    2px 2px 0 #007722,
    4px 4px 0 #000;
}
.pixel-btn.pink:hover {
  transform: translate(2px, 2px);
  box-shadow:
    2px 2px 0 #cc3377,
    4px 4px 0 #000;
}
.pixel-btn.red:hover {
  transform: translate(2px, 2px);
  box-shadow:
    2px 2px 0 #880022,
    4px 4px 0 #000;
}
.pixel-btn.secondary:hover {
  transform: translate(2px, 2px);
  box-shadow:
    2px 2px 0 #111,
    4px 4px 0 #000;
}
.pixel-btn:active {
  transform: translate(4px, 4px);
  box-shadow: none;
}

/* ---- GAME SCREEN COMMON ---- */
.game-screen {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  background: var(--dark);
  padding: 1rem;
  gap: 1rem;
}
.game-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  max-width: 420px;
  padding: 0.5rem 0;
}
.game-header h2 {
  font-size: clamp(0.6rem, 3vw, 0.9rem);
  color: var(--gold);
}
.score-display {
  font-size: clamp(0.5rem, 2.5vw, 0.75rem);
  color: var(--green);
  background: #111;
  padding: 0.4rem 0.8rem;
  border: 2px solid var(--green);
}
.canvas-wrapper {
  position: relative;
  width: 100%;
  max-width: 400px;
  border: 4px solid var(--gold);
  box-shadow:
    0 0 20px rgba(255, 221, 0, 0.3),
    8px 8px 0 #000;
}
.canvas-wrapper canvas {
  display: block;
  width: 100%;
  height: auto;
  image-rendering: pixelated;
}
.canvas-overlay {
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.87);
  display: flex;
  align-items: center;
  justify-content: center;
}
.overlay-content {
  text-align: center;
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
}
.overlay-content p {
  font-size: clamp(0.6rem, 3vw, 1rem);
  color: var(--gold);
}
.overlay-content .small-text {
  font-size: clamp(0.4rem, 2vw, 0.6rem);
  color: #aaa;
}
.overlay-content .red-text {
  color: var(--red);
}
.overlay-content .gold-text {
  color: var(--gold);
}
.overlay-content .green-text {
  color: var(--green);
}
.win-overlay {
  background: rgba(0, 20, 0, 0.92);
}

/* ---- D-PAD ---- */
.mobile-controls {
  display: flex;
  justify-content: center;
  padding: 0.5rem;
}
.dpad {
  display: grid;
  grid-template-rows: 1fr 1fr 1fr;
  gap: 2px;
  align-items: center;
  justify-items: center;
}
.dpad-row {
  display: flex;
  gap: 2px;
  align-items: center;
}
.dpad-btn {
  font-family: 'Press Start 2P', monospace;
  font-size: 0.8rem;
  width: 52px;
  height: 52px;
  background: #333;
  border: none;
  color: #fff;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 2px 2px 0 #111;
  transition: transform 0.05s;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}
.dpad-btn:active {
  transform: translate(2px, 2px);
  box-shadow: none;
}
.dpad-center {
  width: 52px;
  height: 52px;
  background: #222;
}
</style>
