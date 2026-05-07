<script setup>
import { ref, onMounted } from 'vue'

const visible = ref(false)
const activeSection = ref('cover')

// Data pasangan — ganti sesuai info kalian
const groom = {
  name: 'BRYAN',
  fullName: 'Rayhan Rizqi Bebryan',
  parents: 'Putra dari Bpk. ... & Ibu ...',
  emoji: '🤵',
}
const bride = {
  name: 'RIFA',
  fullName: 'Rifa Dian Eka Farah',
  parents: 'Putri dari Bpk. ... & Ibu ...',
  emoji: '👰',
}
const event = {
  akad: { date: 'SABTU, 14 JUNI 2026', time: '08:00 WIB', label: 'AKAD NIKAH' },
  resepsi: { date: 'SABTU, 14 JUNI 2026', time: '11:00 WIB', label: 'RESEPSI' },
  venue: 'Gedung Serbaguna Al-Ikhlas',
  address: 'Jl. Raya Kebayoran No. 12,\nJakarta Selatan',
  maps: 'https://maps.google.com',
}

// Countdown
const countdown = ref({ d: 0, h: 0, m: 0, s: 0 })
function updateCountdown() {
  const target = new Date('2026-06-14T08:00:00')
  const diff = target - new Date()
  if (diff <= 0) {
    countdown.value = { d: 0, h: 0, m: 0, s: 0 }
    return
  }
  countdown.value = {
    d: Math.floor(diff / 86400000),
    h: Math.floor((diff % 86400000) / 3600000),
    m: Math.floor((diff % 3600000) / 60000),
    s: Math.floor((diff % 60000) / 1000),
  }
}

// RSVP
const rsvpName = ref('')
const rsvpAtt = ref('hadir')
const rsvpMsg = ref('')
const rsvpSent = ref(false)
function submitRsvp() {
  if (!rsvpName.value.trim()) return
  rsvpSent.value = true
}

// Gallery — foto dari assets (diproses jadi 8-bit via PixelPhoto)
const photos = [
  {
    src: new URL('../assets/photo1.jpeg', import.meta.url).href,
    caption: 'STAGE 1: FIRST MEET',
    label: '💍',
  },
  {
    src: new URL('../assets/photo2.jpeg', import.meta.url).href,
    caption: 'STAGE 2: ADVENTURE',
    label: '🎮',
  },
]

onMounted(() => {
  setTimeout(() => (visible.value = true), 100)
  updateCountdown()
  setInterval(updateCountdown, 1000)
})

const sections = ['cover', 'couple', 'event', 'gallery', 'rsvp']
</script>

<template>
  <div class="inv-wrap" :class="{ visible }">
    <!-- NAV BAR -->
    <nav class="pixel-nav">
      <button
        v-for="s in sections"
        :key="s"
        class="nav-btn"
        :class="{ active: activeSection === s }"
        @click="activeSection = s"
      >
        {{ s.toUpperCase() }}
      </button>
    </nav>

    <!-- ══════════ COVER ══════════ -->
    <section v-if="activeSection === 'cover'" class="inv-section cover-section">
      <div class="cover-inner">
        <div class="pixel-badge blink">💍 UNLOCKED 💍</div>
        <div class="cover-hearts">
          <span
            v-for="i in 5"
            :key="i"
            class="pixel-heart"
            :style="{ animationDelay: i * 0.2 + 's' }"
            >♥</span
          >
        </div>
        <h1 class="couple-title">
          <span class="groom-name">{{ groom.name }}</span>
          <span class="amp">&</span>
          <span class="bride-name">{{ bride.name }}</span>
        </h1>
        <p class="cover-sub">ARE GETTING MARRIED!</p>
        <div class="pixel-divider">─── ♥ ───────────── ♥ ───</div>
        <div class="countdown-box">
          <p class="cd-label">HITUNG MUNDUR</p>
          <div class="cd-grid">
            <div class="cd-item">
              <span class="cd-num">{{ String(countdown.d).padStart(2, '0') }}</span
              ><span class="cd-unit">HARI</span>
            </div>
            <div class="cd-sep">:</div>
            <div class="cd-item">
              <span class="cd-num">{{ String(countdown.h).padStart(2, '0') }}</span
              ><span class="cd-unit">JAM</span>
            </div>
            <div class="cd-sep">:</div>
            <div class="cd-item">
              <span class="cd-num">{{ String(countdown.m).padStart(2, '0') }}</span
              ><span class="cd-unit">MENIT</span>
            </div>
            <div class="cd-sep">:</div>
            <div class="cd-item">
              <span class="cd-num">{{ String(countdown.s).padStart(2, '0') }}</span
              ><span class="cd-unit">DETIK</span>
            </div>
          </div>
        </div>
        <p class="cover-quote">
          "Dan di antara tanda-tanda kekuasaan-Nya<br />
          ialah Dia menciptakan untukmu pasangan hidup"<br />
          <span style="color: #888; font-size: 0.4rem">— QS. Ar-Rum: 21</span>
        </p>
        <div class="nav-hint blink">▼ SCROLL UNTUK DETAIL ▼</div>
        <button class="pixel-btn gold" style="margin-top: 1rem" @click="activeSection = 'couple'">
          NEXT ▶
        </button>
      </div>
    </section>

    <!-- ══════════ COUPLE ══════════ -->
    <section v-if="activeSection === 'couple'" class="inv-section couple-section">
      <h2 class="section-title">THE COUPLE</h2>
      <div class="couple-cards">
        <!-- Groom -->
        <div class="person-card groom-card">
          <div class="pixel-avatar groom-avatar">
            <!-- 8-bit groom pixel art -->
            <svg viewBox="0 0 32 32" width="96" height="96" style="image-rendering: pixelated">
              <!-- hair -->
              <rect x="10" y="2" width="12" height="4" fill="#2a1a0a" />
              <rect x="8" y="4" width="16" height="4" fill="#2a1a0a" />
              <!-- face -->
              <rect x="8" y="8" width="16" height="12" fill="#f0b080" />
              <!-- eyes -->
              <rect x="11" y="11" width="3" height="3" fill="#1a1a1a" />
              <rect x="18" y="11" width="3" height="3" fill="#1a1a1a" />
              <!-- smile -->
              <rect x="12" y="16" width="8" height="2" fill="#cc6644" />
              <!-- suit -->
              <rect x="6" y="20" width="20" height="12" fill="#1a1a4e" />
              <!-- tie -->
              <rect x="14" y="20" width="4" height="8" fill="#ff0044" />
              <!-- shirt -->
              <rect x="10" y="20" width="4" height="6" fill="#ffffff" />
              <rect x="18" y="20" width="4" height="6" fill="#ffffff" />
            </svg>
          </div>
          <div class="person-label groom-label">GROOM</div>
          <div class="person-name">{{ groom.fullName }}</div>
          <div class="person-parents">{{ groom.parents }}</div>
        </div>

        <div class="versus-badge">
          <div class="vs-heart">♥</div>
          <div class="vs-text">×</div>
        </div>

        <!-- Bride -->
        <div class="person-card bride-card">
          <div class="pixel-avatar bride-avatar">
            <svg viewBox="0 0 32 32" width="96" height="96" style="image-rendering: pixelated">
              <!-- hijab -->
              <rect x="6" y="2" width="20" height="18" fill="#f5c6d0" rx="2" />
              <rect x="4" y="8" width="24" height="12" fill="#f0a8bc" />
              <!-- face -->
              <rect x="9" y="6" width="14" height="12" fill="#f0b080" />
              <!-- eyes -->
              <rect x="11" y="10" width="3" height="3" fill="#1a1a1a" />
              <rect x="18" y="10" width="3" height="3" fill="#1a1a1a" />
              <!-- blush -->
              <rect x="9" y="14" width="3" height="2" fill="#ffaaaa" />
              <rect x="20" y="14" width="3" height="2" fill="#ffaaaa" />
              <!-- smile -->
              <rect x="12" y="15" width="8" height="2" fill="#cc6644" />
              <!-- dress -->
              <rect x="5" y="20" width="22" height="12" fill="#ff69b4" />
              <rect x="9" y="20" width="14" height="4" fill="#ff99cc" />
            </svg>
          </div>
          <div class="person-label bride-label">BRIDE</div>
          <div class="person-name">{{ bride.fullName }}</div>
          <div class="person-parents">{{ bride.parents }}</div>
        </div>
      </div>

      <div class="couple-nav">
        <button class="pixel-btn secondary" @click="activeSection = 'cover'">◀ BACK</button>
        <button class="pixel-btn gold" @click="activeSection = 'event'">NEXT ▶</button>
      </div>
    </section>

    <!-- ══════════ EVENT ══════════ -->
    <section v-if="activeSection === 'event'" class="inv-section event-section">
      <h2 class="section-title">THE EVENT</h2>

      <div class="event-cards">
        <!-- Akad -->
        <div class="event-card">
          <div class="event-icon">🕌</div>
          <div class="event-badge">{{ event.akad.label }}</div>
          <div class="event-date">{{ event.akad.date }}</div>
          <div class="event-time">⏰ {{ event.akad.time }}</div>
        </div>
        <!-- Resepsi -->
        <div class="event-card">
          <div class="event-icon">🎊</div>
          <div class="event-badge resepsi">{{ event.resepsi.label }}</div>
          <div class="event-date">{{ event.resepsi.date }}</div>
          <div class="event-time">⏰ {{ event.resepsi.time }}</div>
        </div>
      </div>

      <!-- Venue -->
      <div class="venue-box">
        <div class="venue-icon">📍</div>
        <div class="venue-name">{{ event.venue }}</div>
        <div class="venue-addr">{{ event.address }}</div>
        <a
          :href="event.maps"
          target="_blank"
          class="pixel-btn green"
          style="margin-top: 1rem; text-decoration: none; font-size: 0.5rem; padding: 0.7rem 1.2rem"
        >
          🗺 BUKA MAPS
        </a>
      </div>

      <!-- Mini map pixel art -->
      <div class="pixel-map">
        <svg viewBox="0 0 160 80" width="100%" style="max-width: 400px; image-rendering: pixelated">
          <rect width="160" height="80" fill="#0a1a0a" />
          <!-- roads -->
          <rect x="0" y="36" width="160" height="8" fill="#333" />
          <rect x="76" y="0" width="8" height="80" fill="#333" />
          <!-- center lines -->
          <rect x="0" y="39" width="30" height="2" fill="#ffdd00" opacity=".5" />
          <rect x="50" y="39" width="20" height="2" fill="#ffdd00" opacity=".5" />
          <rect x="90" y="39" width="20" height="2" fill="#ffdd00" opacity=".5" />
          <rect x="120" y="39" width="40" height="2" fill="#ffdd00" opacity=".5" />
          <!-- buildings -->
          <rect x="10" y="10" width="20" height="24" fill="#1a2a3a" />
          <rect x="40" y="15" width="28" height="19" fill="#2a1a3a" />
          <rect x="110" y="8" width="22" height="26" fill="#1a2a1a" />
          <rect x="138" y="18" width="14" height="16" fill="#2a2a1a" />
          <rect x="10" y="48" width="18" height="20" fill="#2a1a1a" />
          <rect x="40" y="50" width="24" height="18" fill="#1a2a2a" />
          <rect x="110" y="50" width="30" height="20" fill="#2a2a3a" />
          <!-- venue marker -->
          <rect x="70" y="26" width="20" height="18" fill="#ff0044" />
          <rect x="74" y="22" width="12" height="6" fill="#ff0044" />
          <rect x="78" y="18" width="4" height="6" fill="#ff0044" />
          <rect x="72" y="28" width="4" height="4" fill="#fff" opacity=".7" />
          <rect x="78" y="28" width="8" height="10" fill="#fff" opacity=".4" />
          <!-- YOU ARE HERE label -->
          <text
            x="80"
            y="78"
            fill="#ffdd00"
            font-size="5"
            text-anchor="middle"
            font-family="monospace"
          >
            📍 VENUE
          </text>
        </svg>
      </div>

      <div class="couple-nav">
        <button class="pixel-btn secondary" @click="activeSection = 'couple'">◀ BACK</button>
        <button class="pixel-btn gold" @click="activeSection = 'gallery'">NEXT ▶</button>
      </div>
    </section>

    <!-- ══════════ GALLERY ══════════ -->
    <section v-if="activeSection === 'gallery'" class="inv-section gallery-section">
      <h2 class="section-title">OUR STORY</h2>
      <p class="section-sub">SELECT STAGE TO VIEW</p>

      <div class="gallery-grid">
        <div v-for="(p, i) in photos" :key="i" class="gallery-item">
          <div class="photo-frame">
            <img :src="p.src" :alt="p.caption" class="photo-img" />
            <div class="photo-overlay-badge">{{ p.label }}</div>
          </div>
          <div class="photo-caption">{{ p.caption }}</div>
          <div class="photo-badge">STAGE {{ i + 1 }}</div>
        </div>
      </div>

      <!-- Pixel decoration -->
      <div class="gallery-deco">
        <span
          v-for="c in ['♥', '★', '♦', '♠', '♣', '★', '♥']"
          :key="c + Math.random()"
          class="deco-char"
          >{{ c }}</span
        >
      </div>

      <div class="couple-nav">
        <button class="pixel-btn secondary" @click="activeSection = 'event'">◀ BACK</button>
        <button class="pixel-btn gold" @click="activeSection = 'rsvp'">NEXT ▶</button>
      </div>
    </section>

    <!-- ══════════ RSVP ══════════ -->
    <section v-if="activeSection === 'rsvp'" class="inv-section rsvp-section">
      <h2 class="section-title">RSVP</h2>
      <p class="section-sub">KONFIRMASI KEHADIRAN</p>

      <div v-if="!rsvpSent" class="rsvp-form">
        <div class="form-group">
          <label>NAMA TAMU</label>
          <input
            v-model="rsvpName"
            class="pixel-input"
            placeholder="Masukkan nama..."
            maxlength="40"
          />
        </div>
        <div class="form-group">
          <label>KEHADIRAN</label>
          <div class="radio-group">
            <label class="radio-option" :class="{ active: rsvpAtt === 'hadir' }">
              <input type="radio" v-model="rsvpAtt" value="hadir" hidden />
              <span>✅ HADIR</span>
            </label>
            <label class="radio-option" :class="{ active: rsvpAtt === 'tidak' }">
              <input type="radio" v-model="rsvpAtt" value="tidak" hidden />
              <span>❌ TIDAK HADIR</span>
            </label>
          </div>
        </div>
        <div class="form-group">
          <label>UCAPAN / DOA</label>
          <textarea
            v-model="rsvpMsg"
            class="pixel-input"
            rows="3"
            placeholder="Tulis ucapan selamat..."
            maxlength="200"
          ></textarea>
        </div>
        <button class="pixel-btn gold" style="width: 100%; margin-top: 0.5rem" @click="submitRsvp">
          💌 KIRIM RSVP
        </button>
      </div>

      <!-- Sent confirmation -->
      <div v-else class="rsvp-success">
        <div class="success-icon blink">🎉</div>
        <p class="success-title green-text">TERIMA KASIH!</p>
        <p class="success-msg">
          <span style="color: #ffdd00">{{ rsvpName }}</span
          ><br />
          <span v-if="rsvpAtt === 'hadir'">Kami tunggu kehadiranmu! ♥</span>
          <span v-else>Terima kasih atas doanya 🤲</span>
        </p>
        <p v-if="rsvpMsg" class="rsvp-quote">"{{ rsvpMsg }}"</p>
      </div>

      <!-- Footer -->
      <div class="inv-footer">
        <div class="footer-hearts">♥ ♥ ♥ ♥ ♥</div>
        <p class="footer-names">{{ groom.name }} ♥ {{ bride.name }}</p>
        <p class="footer-date">{{ event.akad.date }}</p>
        <div class="footer-copy">© 2026 PIXEL WEDDING STUDIO</div>
      </div>

      <div class="couple-nav">
        <button class="pixel-btn secondary" @click="activeSection = 'gallery'">◀ BACK</button>
        <button class="pixel-btn gold" @click="activeSection = 'cover'">↑ TOP</button>
      </div>
    </section>
  </div>
</template>

<style scoped>
/* ── WRAPPER ── */
.inv-wrap {
  min-height: 100vh;
  background: var(--darker, #050510);
  opacity: 0;
  transform: translateY(20px);
  transition:
    opacity 0.6s,
    transform 0.6s;
}
.inv-wrap.visible {
  opacity: 1;
  transform: translateY(0);
}

/* ── NAV ── */
.pixel-nav {
  position: sticky;
  top: 0;
  z-index: 100;
  display: flex;
  overflow-x: auto;
  gap: 0;
  background: #0a0a1a;
  border-bottom: 3px solid #ffdd00;
  scrollbar-width: none;
}
.pixel-nav::-webkit-scrollbar {
  display: none;
}
.nav-btn {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.35rem, 1.8vw, 0.5rem);
  padding: 0.7rem 0.9rem;
  background: transparent;
  border: none;
  color: #666;
  cursor: pointer;
  border-bottom: 3px solid transparent;
  white-space: nowrap;
  flex-shrink: 0;
  transition: color 0.15s;
}
.nav-btn.active {
  color: #ffdd00;
  border-bottom-color: #ffdd00;
}
.nav-btn:hover:not(.active) {
  color: #aaa;
}

/* ── SECTIONS ── */
.inv-section {
  min-height: calc(100vh - 46px);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 2rem 1rem;
  gap: 1.5rem;
  max-width: 700px;
  margin: 0 auto;
  width: 100%;
}

.section-title {
  font-size: clamp(1rem, 5vw, 2rem);
  color: #ffdd00;
  text-shadow:
    3px 3px 0 #ff6600,
    6px 6px 0 #000;
  text-align: center;
}
.section-sub {
  color: #888;
  font-size: clamp(0.38rem, 2vw, 0.55rem);
  text-align: center;
}

/* ── COVER ── */
.cover-section {
  justify-content: center;
  text-align: center;
}
.cover-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.2rem;
  max-width: 500px;
  width: 100%;
}
.pixel-badge {
  background: #ff69b4;
  color: #000;
  font-size: clamp(0.4rem, 2vw, 0.6rem);
  padding: 0.5rem 1rem;
  box-shadow:
    3px 3px 0 #880044,
    5px 5px 0 #000;
}
.cover-hearts {
  display: flex;
  gap: 0.5rem;
}
.pixel-heart {
  color: #ff0044;
  font-size: 1.4rem;
  animation: heartbeat 1.5s ease-in-out infinite;
}
.couple-title {
  line-height: 1.4;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
}
.groom-name {
  font-size: clamp(1.5rem, 8vw, 3.5rem);
  color: #00aaff;
  text-shadow:
    3px 3px 0 #004488,
    6px 6px 0 #000;
}
.amp {
  font-size: clamp(0.8rem, 4vw, 1.5rem);
  color: #ffdd00;
}
.bride-name {
  font-size: clamp(1.5rem, 8vw, 3.5rem);
  color: #ff69b4;
  text-shadow:
    3px 3px 0 #880044,
    6px 6px 0 #000;
}
.cover-sub {
  color: #ffdd00;
  font-size: clamp(0.45rem, 2.5vw, 0.7rem);
  letter-spacing: 0.15em;
}
.pixel-divider {
  color: #ff69b4;
  font-size: clamp(0.5rem, 2.5vw, 0.7rem);
  letter-spacing: 0.1em;
}
.cover-quote {
  color: #888;
  font-size: clamp(0.38rem, 1.8vw, 0.52rem);
  line-height: 2.2;
  font-style: normal;
}
.nav-hint {
  color: #444;
  font-size: 0.4rem;
  margin-top: 0.5rem;
}

/* ── COUNTDOWN ── */
.countdown-box {
  background: #0d0d22;
  border: 3px solid #ffdd00;
  box-shadow:
    4px 4px 0 #ff6600,
    8px 8px 0 #000;
  padding: 1.2rem 1.5rem;
  width: 100%;
}
.cd-label {
  font-size: 0.5rem;
  color: #888;
  margin-bottom: 0.8rem;
  text-align: center;
}
.cd-grid {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}
.cd-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
}
.cd-num {
  font-size: clamp(1.2rem, 6vw, 2.2rem);
  color: #ffdd00;
  text-shadow: 2px 2px 0 #ff6600;
}
.cd-unit {
  font-size: 0.35rem;
  color: #666;
}
.cd-sep {
  font-size: clamp(0.8rem, 4vw, 1.5rem);
  color: #ff0044;
  align-self: flex-start;
  margin-top: 0.2rem;
}

/* ── COUPLE ── */
.couple-cards {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  flex-wrap: wrap;
  width: 100%;
}
.person-card {
  background: #0d0d22;
  border: 3px solid #00aaff;
  box-shadow:
    5px 5px 0 #004488,
    8px 8px 0 #000;
  padding: 1.5rem 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.7rem;
  flex: 1;
  min-width: 150px;
  max-width: 220px;
  text-align: center;
}
.bride-card {
  border-color: #ff69b4;
  box-shadow:
    5px 5px 0 #880044,
    8px 8px 0 #000;
}
.pixel-avatar {
  image-rendering: pixelated;
}
.person-label {
  font-size: 0.5rem;
  padding: 0.3rem 0.8rem;
  background: #00aaff;
  color: #000;
  box-shadow: 2px 2px 0 #004488;
}
.bride-card .person-label {
  background: #ff69b4;
  box-shadow: 2px 2px 0 #880044;
}
.person-name {
  font-size: clamp(0.45rem, 2.5vw, 0.65rem);
  color: #fff;
  line-height: 1.8;
}
.person-parents {
  font-size: clamp(0.35rem, 1.8vw, 0.48rem);
  color: #666;
  line-height: 2;
}
.versus-badge {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
}
.vs-heart {
  font-size: 2rem;
  color: #ff0044;
  animation: heartbeat 1.5s ease-in-out infinite;
}
.vs-text {
  font-size: 0.8rem;
  color: #888;
}

/* ── EVENT ── */
.event-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 1rem;
  width: 100%;
}
.event-card {
  background: #0d0d22;
  border: 3px solid #ffdd00;
  box-shadow:
    5px 5px 0 #ff6600,
    8px 8px 0 #000;
  padding: 1.5rem 1rem;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.8rem;
}
.event-icon {
  font-size: 2.5rem;
}
.event-badge {
  font-size: 0.5rem;
  background: #ffdd00;
  color: #000;
  padding: 0.4rem 0.8rem;
  box-shadow: 2px 2px 0 #ff6600;
}
.event-badge.resepsi {
  background: #ff69b4;
  box-shadow: 2px 2px 0 #880044;
}
.event-date {
  font-size: clamp(0.4rem, 2vw, 0.6rem);
  color: #fff;
  line-height: 1.8;
}
.event-time {
  font-size: clamp(0.4rem, 2vw, 0.55rem);
  color: #00ff41;
}
.venue-box {
  background: #0d0d22;
  border: 3px solid #00ff41;
  box-shadow:
    5px 5px 0 #007722,
    8px 8px 0 #000;
  padding: 1.5rem;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.7rem;
  text-align: center;
}
.venue-icon {
  font-size: 2rem;
}
.venue-name {
  font-size: clamp(0.55rem, 3vw, 0.8rem);
  color: #ffdd00;
}
.venue-addr {
  font-size: clamp(0.38rem, 2vw, 0.55rem);
  color: #888;
  line-height: 2;
  white-space: pre-line;
}
.pixel-map {
  width: 100%;
  display: flex;
  justify-content: center;
}

/* ── GALLERY ── */
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
  gap: 1.5rem;
  width: 100%;
}
.gallery-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}
.photo-frame {
  width: 100%;
  aspect-ratio: 1;
  border: 4px solid #ffdd00;
  box-shadow:
    4px 4px 0 #ff6600,
    8px 8px 0 #000;
  overflow: hidden;
  position: relative;
  background: #0d0d22;
}
.photo-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}
.photo-caption {
  font-size: clamp(0.38rem, 2vw, 0.55rem);
  color: #ffdd00;
  text-align: center;
}
.photo-badge {
  font-size: 0.4rem;
  background: #ff69b4;
  color: #000;
  padding: 0.25rem 0.6rem;
  box-shadow: 2px 2px 0 #880044;
}
.photo-overlay-badge {
  position: absolute;
  bottom: 6px;
  right: 6px;
  font-size: 1.2rem;
  filter: drop-shadow(2px 2px 0 #000);
  pointer-events: none;
}
.gallery-deco {
  display: flex;
  gap: 1rem;
  justify-content: center;
  font-size: 1rem;
  color: #333;
  flex-wrap: wrap;
}
.deco-char {
  animation: heartbeat 2s ease-in-out infinite;
}

/* ── RSVP ── */
.rsvp-form {
  background: #0d0d22;
  border: 3px solid #ff69b4;
  box-shadow:
    5px 5px 0 #880044,
    8px 8px 0 #000;
  padding: 1.5rem;
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}
.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}
.form-group label {
  font-size: 0.5rem;
  color: #ff69b4;
}
.pixel-input {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.4rem, 2vw, 0.55rem);
  background: #060610;
  color: #fff;
  border: 2px solid #ff69b4;
  padding: 0.7rem;
  outline: none;
  resize: vertical;
  width: 100%;
  line-height: 1.8;
}
.pixel-input:focus {
  border-color: #ffdd00;
}
.pixel-input::placeholder {
  color: #444;
}
.radio-group {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}
.radio-option {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.4rem, 2vw, 0.55rem);
  padding: 0.5rem 0.8rem;
  background: #111;
  border: 2px solid #333;
  cursor: pointer;
  transition:
    border-color 0.1s,
    background 0.1s;
}
.radio-option.active {
  border-color: #ffdd00;
  background: #1a1a00;
  color: #ffdd00;
}

/* RSVP Success */
.rsvp-success {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  background: #0d0d22;
  border: 3px solid #00ff41;
  box-shadow:
    5px 5px 0 #007722,
    8px 8px 0 #000;
  padding: 2rem;
  width: 100%;
}
.success-icon {
  font-size: 3rem;
}
.success-title {
  font-size: 1.2rem;
}
.success-msg {
  font-size: clamp(0.45rem, 2.5vw, 0.65rem);
  color: #fff;
  line-height: 2.2;
}
.rsvp-quote {
  font-size: clamp(0.4rem, 2vw, 0.55rem);
  color: #888;
  font-style: italic;
  margin-top: 0.5rem;
}
.green-text {
  color: #00ff41;
}

/* ── FOOTER ── */
.inv-footer {
  text-align: center;
  margin-top: 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.6rem;
  padding: 1.5rem;
  border-top: 2px solid #1a1a2e;
  width: 100%;
}
.footer-hearts {
  color: #ff69b4;
  font-size: 0.8rem;
  letter-spacing: 0.5rem;
  animation: heartbeat 1.5s ease-in-out infinite;
}
.footer-names {
  font-size: clamp(0.6rem, 3vw, 0.9rem);
  color: #ffdd00;
}
.footer-date {
  font-size: clamp(0.38rem, 2vw, 0.55rem);
  color: #888;
}
.footer-copy {
  font-size: 0.35rem;
  color: #333;
  margin-top: 0.5rem;
}

/* ── NAV BUTTONS ── */
.couple-nav {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-top: 1rem;
  flex-wrap: wrap;
}

/* ── ANIMATIONS ── */
@keyframes heartbeat {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
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
