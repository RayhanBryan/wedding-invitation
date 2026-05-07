<script setup>
defineEmits(['select'])

const games = [
  {
    id: 'snake',
    icon: '🐍',
    name: 'SNAKE',
    desc: 'Makan 10 makanan\nuntuk menang!',
    controls: '← → ↑ ↓ / WASD',
    diff: 2,
    color: '#00ff41',
    shadow: '#007722',
  },
  {
    id: 'memory',
    icon: '🃏',
    name: 'MEMORY',
    desc: 'Cocokkan semua\npasangan kartu!',
    controls: 'Klik / Tap kartu',
    diff: 1,
    color: '#ff69b4',
    shadow: '#cc3377',
  },
  {
    id: 'flappy',
    icon: '🧺',
    name: 'CATCH ♥',
    desc: 'Tangkap 15 hati\nyang jatuh!',
    controls: '← → / Drag layar',
    diff: 1,
    color: '#ffdd00',
    shadow: '#ff6600',
  },
]
</script>

<template>
  <div class="selector-screen">
    <div class="selector-inner">
      <h2 class="selector-title">SELECT GAME</h2>
      <p class="selector-sub">Selesaikan game untuk<br />membuka undangan 💍</p>

      <div class="game-cards">
        <button
          v-for="g in games"
          :key="g.id"
          class="game-card"
          :style="{ '--card-color': g.color, '--card-shadow': g.shadow }"
          @click="$emit('select', g.id)"
        >
          <div class="card-icon">{{ g.icon }}</div>
          <div class="card-name" :style="{ color: g.color }">{{ g.name }}</div>
          <div class="card-desc">{{ g.desc }}</div>
          <div class="card-controls">{{ g.controls }}</div>
          <div class="card-diff">
            <span class="diff-label">LEVEL:</span>
            <span
              v-for="n in 3"
              :key="n"
              class="diff-star"
              :style="{ color: n <= g.diff ? g.color : '#333' }"
              >★</span
            >
          </div>
          <div class="card-play blink" :style="{ color: g.color }">▶ PLAY</div>
        </button>
      </div>

      <p class="selector-hint blink">♥ Pilih salah satu game ♥</p>
    </div>
  </div>
</template>

<style scoped>
.selector-screen {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: radial-gradient(ellipse at center, #1a0a2e 0%, #0a0a1a 70%);
  padding: 2rem 1rem;
}
.selector-inner {
  width: 100%;
  max-width: 700px;
  text-align: center;
}
.selector-title {
  font-size: clamp(1.2rem, 6vw, 2.5rem);
  color: #ffdd00;
  text-shadow:
    4px 4px 0 #ff6600,
    8px 8px 0 #000;
  margin-bottom: 0.8rem;
}
.selector-sub {
  color: #888;
  font-size: clamp(0.4rem, 2vw, 0.6rem);
  line-height: 2.2;
  margin-bottom: 2rem;
}
.game-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}
.game-card {
  font-family: 'Press Start 2P', monospace;
  background: #111;
  border: 3px solid var(--card-color);
  box-shadow:
    6px 6px 0 var(--card-shadow),
    10px 10px 0 #000;
  padding: 1.5rem 1rem;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  align-items: center;
  transition:
    transform 0.1s,
    box-shadow 0.1s;
}
.game-card:hover {
  transform: translate(-3px, -3px);
  box-shadow:
    9px 9px 0 var(--card-shadow),
    13px 13px 0 #000;
}
.game-card:active {
  transform: translate(4px, 4px);
  box-shadow:
    2px 2px 0 var(--card-shadow),
    4px 4px 0 #000;
}
.card-icon {
  font-size: 2.5rem;
}
.card-name {
  font-size: 1rem;
  text-shadow: 2px 2px 0 #000;
}
.card-desc {
  font-size: 0.45rem;
  color: #aaa;
  line-height: 2;
  white-space: pre-line;
  text-align: center;
}
.card-controls {
  font-size: 0.38rem;
  color: #666;
  background: #0a0a0a;
  padding: 0.4rem 0.6rem;
  border: 1px solid #333;
  width: 100%;
  text-align: center;
}
.card-diff {
  display: flex;
  align-items: center;
  gap: 0.3rem;
}
.diff-label {
  font-size: 0.4rem;
  color: #555;
}
.diff-star {
  font-size: 0.8rem;
}
.card-play {
  font-size: 0.65rem;
  margin-top: 0.25rem;
  letter-spacing: 0.15em;
}
.selector-hint {
  font-size: 0.5rem;
  color: #ff69b4;
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
</style>
