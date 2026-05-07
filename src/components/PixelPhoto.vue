<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  src: { type: String, required: true },
  pixelSize: { type: Number, default: 6 }, // ukuran pixel blok (makin besar makin 8-bit)
  colors: { type: Number, default: 24 }, // jumlah warna palette
  width: { type: Number, default: 240 },
  height: { type: Number, default: 240 },
})

const canvas = ref(null)
const loading = ref(true)
const error = ref(false)

// Quantize satu channel ke N steps
function quantize(val, steps) {
  const step = 255 / (steps - 1)
  return Math.round(Math.round(val / step) * step)
}

// Pilih warna terdekat dari palette retro 8-bit
const PALETTE = [
  // Hitam, putih, abu
  [0, 0, 0],
  [255, 255, 255],
  [85, 85, 85],
  [170, 170, 170],
  // Merah-pink
  [255, 0, 68],
  [255, 85, 85],
  [255, 170, 170],
  [204, 0, 102],
  [255, 105, 180],
  // Kuning-emas
  [255, 221, 0],
  [255, 170, 0],
  [255, 102, 0],
  [204, 136, 0],
  // Hijau
  [0, 255, 65],
  [0, 204, 51],
  [0, 136, 34],
  [0, 85, 17],
  // Biru
  [0, 170, 255],
  [0, 85, 204],
  [0, 34, 136],
  [170, 221, 255],
  // Ungu
  [136, 0, 255],
  [68, 0, 170],
  [204, 136, 255],
  // Coklat-kulit
  [240, 176, 128],
  [210, 140, 80],
  [150, 90, 40],
  [80, 40, 10],
]

function nearestPalette(r, g, b) {
  let best = 0,
    bestDist = Infinity
  for (let i = 0; i < PALETTE.length; i++) {
    const [pr, pg, pb] = PALETTE[i]
    const d = (r - pr) ** 2 + (g - pg) ** 2 + (b - pb) ** 2
    if (d < bestDist) {
      bestDist = d
      best = i
    }
  }
  return PALETTE[best]
}

function applyPixelEffect(srcCanvas) {
  const W = props.width,
    H = props.height
  const ps = props.pixelSize

  // Downscale dulu ke low-res
  const tiny = document.createElement('canvas')
  tiny.width = Math.floor(W / ps)
  tiny.height = Math.floor(H / ps)
  const tctx = tiny.getContext('2d')
  tctx.imageSmoothingEnabled = true
  tctx.drawImage(srcCanvas, 0, 0, tiny.width, tiny.height)

  // Ambil pixels dan palette-quantize
  const imgData = tctx.getImageData(0, 0, tiny.width, tiny.height)
  const d = imgData.data
  for (let i = 0; i < d.length; i += 4) {
    const [r, g, b] = nearestPalette(d[i], d[i + 1], d[i + 2])
    d[i] = r
    d[i + 1] = g
    d[i + 2] = b
  }
  tctx.putImageData(imgData, 0, 0)

  // Upscale ke ukuran asli — pixelated
  const out = canvas.value
  const octx = out.getContext('2d')
  octx.imageSmoothingEnabled = false
  octx.clearRect(0, 0, W, H)
  octx.drawImage(tiny, 0, 0, W, H)

  // Overlay scanline ringan
  octx.globalAlpha = 0.08
  for (let y = 0; y < H; y += 2) {
    octx.fillStyle = '#000'
    octx.fillRect(0, y, W, 1)
  }
  octx.globalAlpha = 1

  // Vignette
  const vg = octx.createRadialGradient(W / 2, H / 2, H * 0.3, W / 2, H / 2, H * 0.75)
  vg.addColorStop(0, 'rgba(0,0,0,0)')
  vg.addColorStop(1, 'rgba(0,0,0,0.45)')
  octx.fillStyle = vg
  octx.fillRect(0, 0, W, H)
}

function processImage() {
  loading.value = true
  error.value = false
  const img = new Image()
  img.crossOrigin = 'anonymous'
  img.onload = () => {
    // Draw ke temp canvas dengan aspect-ratio cover
    const tmp = document.createElement('canvas')
    tmp.width = props.width
    tmp.height = props.height
    const tc = tmp.getContext('2d')
    const scale = Math.max(props.width / img.width, props.height / img.height)
    const sw = img.width * scale,
      sh = img.height * scale
    const sx = (props.width - sw) / 2
    const sy = (props.height - sh) / 2
    tc.drawImage(img, sx, sy, sw, sh)
    applyPixelEffect(tmp)
    loading.value = false
  }
  img.onerror = () => {
    loading.value = false
    error.value = true
  }
  img.src = props.src
}

onMounted(processImage)
watch(() => props.src, processImage)
</script>

<template>
  <div class="pixel-photo-wrap" :style="{ width: width + 'px', height: height + 'px' }">
    <!-- Loading shimmer -->
    <div v-if="loading" class="photo-shimmer">
      <svg
        :viewBox="`0 0 ${width} ${height}`"
        :width="width"
        :height="height"
        style="image-rendering: pixelated"
      >
        <rect :width="width" :height="height" fill="#0d0d22" />
        <!-- animated loading bars -->
        <rect x="20%" y="40%" width="60%" height="8" fill="#1a1a44" rx="0" />
        <rect x="30%" y="55%" width="40%" height="6" fill="#1a1a44" rx="0" />
        <text
          :x="width / 2"
          :y="height / 2 - 10"
          fill="#333"
          font-size="10"
          text-anchor="middle"
          font-family="monospace"
        >
          LOADING...
        </text>
      </svg>
    </div>
    <!-- Error fallback: pixel art couple silhouette -->
    <div v-else-if="error" class="photo-fallback-wrap">
      <svg
        :viewBox="`0 0 ${width} ${height}`"
        :width="width"
        :height="height"
        style="image-rendering: pixelated"
      >
        <rect :width="width" :height="height" fill="#0d0d22" />
        <rect x="10%" y="10%" width="80%" height="80%" fill="#1a0a3e" />
        <!-- Groom silhouette -->
        <rect
          :x="width * 0.22"
          :y="height * 0.2"
          :width="width * 0.14"
          :height="height * 0.14"
          fill="#ffdd00"
        />
        <rect
          :x="width * 0.19"
          :y="height * 0.34"
          :width="width * 0.2"
          :height="height * 0.32"
          fill="#1a1a4e"
        />
        <rect
          :x="width * 0.24"
          :y="height * 0.34"
          :width="width * 0.06"
          :height="height * 0.1"
          fill="#fff"
        />
        <rect
          :x="width * 0.27"
          :y="height * 0.34"
          :width="width * 0.05"
          :height="height * 0.14"
          fill="#ff0044"
        />
        <!-- Bride silhouette -->
        <rect
          :x="width * 0.54"
          :y="height * 0.18"
          :width="width * 0.15"
          :height="height * 0.16"
          fill="#f0b080"
        />
        <rect
          :x="width * 0.5"
          :y="height * 0.14"
          :width="width * 0.23"
          :height="height * 0.2"
          fill="#ff99cc"
        />
        <rect
          :x="width * 0.47"
          :y="height * 0.34"
          :width="width * 0.28"
          :height="height * 0.32"
          fill="#ff69b4"
        />
        <!-- Hearts -->
        <text
          :x="width * 0.5"
          :y="height * 0.82"
          fill="#ff0044"
          :font-size="width * 0.07"
          text-anchor="middle"
        >
          ♥
        </text>
        <text
          :x="width * 0.5"
          :y="height * 0.94"
          fill="#555"
          :font-size="width * 0.04"
          text-anchor="middle"
          font-family="monospace"
        >
          NO PHOTO
        </text>
      </svg>
    </div>
    <!-- Actual pixelated canvas -->
    <canvas
      v-show="!loading && !error"
      ref="canvas"
      :width="width"
      :height="height"
      style="image-rendering: pixelated; display: block; width: 100%; height: 100%"
    ></canvas>
  </div>
</template>

<style scoped>
.pixel-photo-wrap {
  position: relative;
  overflow: hidden;
  background: #0d0d22;
}
.photo-shimmer,
.photo-fallback-wrap {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
.photo-shimmer {
  animation: shimmer 1.5s ease-in-out infinite;
}
@keyframes shimmer {
  0%,
  100% {
    opacity: 0.6;
  }
  50% {
    opacity: 1;
  }
}
</style>
