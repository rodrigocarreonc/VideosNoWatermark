<script setup>
import { ref, computed } from 'vue'
import axios from 'axios'

// Variables de estado
const platform = ref('instagram') // Por defecto inicia en TikTok
const videoUrlInput = ref('')
const cleanVideoUrl = ref(null)
const videoTitle = ref('')
const isLoading = ref(false)

// Textos dinámicos según la plataforma elegida
const inputPlaceholder = computed(() => {
  return platform.value === 'instagram'
    ? 'https://www.instagram.com/reel/C123456789/'
    : 'https://www.tiktok.com/@usuario/video/123456789'
})

// Función para alternar la plataforma
const togglePlatform = (selected) => {
  platform.value = selected
  // Limpiamos los campos al cambiar de red social
  videoUrlInput.value = ''
  cleanVideoUrl.value = null
}

// Función para obtener el enlace
const fetchVideo = async () => {
  if (!videoUrlInput.value) return

  isLoading.value = true
  try {
    // Usamos platform.value para dirigir la petición al endpoint correcto (/api/tiktok/convert o /api/instagram/convert)
    const response = await axios.post(
      `${import.meta.env.VITE_API_URL}/api/${platform.value}/convert`,
      {
        url: videoUrlInput.value,
      },
    )

    cleanVideoUrl.value = response.data.video_url
    videoTitle.value = response.data.title || `${platform.value}-video`

    triggerDownload() // Llamar a la función para forzar la descarga
  } catch (error) {
    console.error(error)
    alert(`Error al procesar el video de ${platform.value === 'tiktok' ? 'TikTok' : 'Instagram'}`)
  } finally {
    isLoading.value = false
  }
}

// Función para forzar la descarga
const triggerDownload = () => {
  if (!cleanVideoUrl.value) return
  const safeUrl = encodeURIComponent(cleanVideoUrl.value)
  const safeTitle = encodeURIComponent(videoTitle.value)

  // Usamos platform.value para el proxy de descarga
  window.location.href = `${import.meta.env.VITE_API_URL}/api/${platform.value}/download?url=${safeUrl}&title=${safeTitle}`
}
</script>

<template>
  <div class="page-wrapper">
    <!-- Encabezado Oscuro -->
    <header class="dark-header">
      <div class="container nav-bar">
        <div class="logo">
          <!-- Ícono de ejemplo y nombre -->
          <span class="logo-icon">🔄</span>
          <span class="logo-text">VideosNoWatermark</span>
        </div>
      </div>
    </header>

    <!-- Contenido Principal -->
    <main class="main-content">
      <!-- Tarjeta del Buscador Superpuesta -->
      <div class="search-card">
        <!-- Toggle de Plataformas -->
        <div class="platform-toggle">
          <button
            type="button"
            :class="['toggle-btn', { active: platform === 'instagram' }]"
            @click="togglePlatform('instagram')"
          >
            Instagram Reels
          </button>
          <button
            type="button"
            :class="['toggle-btn', { active: platform === 'tiktok' }]"
            @click="togglePlatform('tiktok')"
          >
            TikTok
          </button>
        </div>

        <label class="input-label"
          >Pega tu enlace de {{ platform === 'tiktok' ? 'TikTok' : 'Instagram' }}</label
        >

        <form @submit.prevent="fetchVideo">
          <input
            type="url"
            class="url-input"
            v-model="videoUrlInput"
            :placeholder="inputPlaceholder"
            required
          />

          <button type="submit" class="convert-btn" :disabled="isLoading">
            {{ isLoading ? 'Procesando...' : 'Descargar' }}
          </button>
        </form>

        <!-- Botón extra que aparece cuando el video está listo -->
        <div v-if="cleanVideoUrl" class="download-ready-section">
          <hr class="divider" />
          <p class="success-text">
            ¡Video procesado con éxito!<br />El video se descargará automaticamente
          </p>
        </div>
      </div>

      <!-- Sección de Texto Explicativo -->
      <section class="info-section container">
        <h1>Descargador de videos de TikTok e Instagram sin Marca de Agua</h1>
        <p>
          VideosNoWatermark es una forma sencilla de descargar videos de TikTok e Instagram como
          archivos MP4 sin marca de agua. Es gratuito y no requiere registro. Está optimizado para
          funcionar en todos los dispositivos, ya sea tu computadora, tableta o teléfono móvil.
        </p>

        <h3>Cómo descargar un video de {{ platform === 'tiktok' ? 'TikTok' : 'Instagram' }}</h3>
        <ol class="instructions-list">
          <li>
            <strong>1.</strong> Entra en la app de
            {{ platform === 'tiktok' ? 'TikTok' : 'Instagram' }} y busca el video que quieras
            descargar.
          </li>
          <li><strong>2.</strong> Toca el botón de "Compartir" y selecciona "Copiar enlace".</li>
          <li><strong>3.</strong> Pega el enlace en nuestro descargador que aparece arriba.</li>
          <li><strong>4.</strong> Haz clic en el botón rojo para iniciar el proceso.</li>
          <li><strong>5.</strong> Guarda el archivo MP4 en tu dispositivo.</li>
        </ol>
      </section>
    </main>
  </div>
</template>

<style scoped>
/* Reset básico para el componente */
* {
  box-sizing: border-box;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.page-wrapper {
  background-color: #f9f9f9;
  min-height: 100vh;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 20px;
}

/* --- ENCABEZADO --- */
.dark-header {
  background-color: #1a1a1a;
  color: white;
  padding: 20px 0 120px 0;
}

.nav-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 24px;
  font-weight: bold;
}

.logo-icon {
  color: #ff3b5c;
}

.nav-links a {
  color: #ccc;
  text-decoration: none;
  margin-left: 20px;
  font-size: 14px;
  transition: color 0.2s;
}

.nav-links a:hover {
  color: white;
}

/* --- TARJETA DE BÚSQUEDA --- */
.main-content {
  padding-bottom: 50px;
}

.search-card {
  background: white;
  max-width: 750px;
  margin: -80px auto 40px auto;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  width: 90%;
}

/* --- TOGGLE DE PLATAFORMAS --- */
.platform-toggle {
  display: flex;
  background-color: #f1f1f1;
  border-radius: 8px;
  margin-bottom: 25px;
  padding: 4px;
  width: 100%;
}

.toggle-btn {
  flex: 1;
  padding: 12px;
  border: none;
  background: transparent;
  font-size: 15px;
  font-weight: 600;
  color: #666;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.toggle-btn.active {
  background-color: white;
  color: #1a1a1a;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.input-label {
  display: block;
  font-weight: 600;
  margin-bottom: 10px;
  color: #333;
}

.url-input {
  width: 100%;
  padding: 15px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-bottom: 20px;
  outline: none;
  transition: border-color 0.2s;
}

.url-input:focus {
  border-color: #ff3b5c;
}

.actions-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.convert-btn {
  background-color: #d9534f;
  color: white;
  border: none;
  padding: 12px 30px;
  font-size: 16px;
  font-weight: bold;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.convert-btn:hover {
  background-color: #c9302c;
}

.convert-btn:disabled {
  background-color: #e59795;
  cursor: not-allowed;
}

/* --- SECCIÓN DE DESCARGA LISTA --- */
.download-ready-section {
  margin-top: 25px;
  text-align: center;
}

.divider {
  border: 0;
  height: 1px;
  background: #eee;
  margin-bottom: 20px;
}

.success-text {
  color: #28a745;
  font-weight: 600;
  margin-bottom: 15px;
}

/* --- SECCIÓN DE TEXTO --- */
.info-section h1 {
  font-size: 24px;
  color: #333;
  margin-bottom: 15px;
}

.info-section p {
  color: #555;
  line-height: 1.6;
  margin-bottom: 30px;
}

.info-section h3 {
  font-size: 20px;
  color: #333;
  margin-bottom: 15px;
}

.instructions-list {
  list-style: none;
  padding: 0;
  color: #555;
}

.instructions-list li {
  margin-bottom: 10px;
  line-height: 1.5;
}
</style>
