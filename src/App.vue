<script setup>
import { ref } from 'vue'
import axios from 'axios'

// Variables de estado
const videoUrlInput = ref('')
const cleanVideoUrl = ref(null)
const videoTitle = ref('')
const isLoading = ref(false)

// Función para obtener el enlace
const fetchVideo = async () => {
  if (!videoUrlInput.value) return

  isLoading.value = true
  try {
    const response = await axios.post(`${import.meta.env.VITE_API_URL}/api/tiktok/convert`, {
      url: videoUrlInput.value,
    })

    cleanVideoUrl.value = response.data.video_url
    videoTitle.value = response.data.title || 'tiktok-video'

    triggerDownload() // Llamar a la función para forzar la descarga
  } catch (error) {
    console.error(error)
    alert('Error al procesar el video')
  } finally {
    isLoading.value = false
  }
}

// Función para forzar la descarga
const triggerDownload = () => {
  if (!cleanVideoUrl.value) return
  const safeUrl = encodeURIComponent(cleanVideoUrl.value)
  const safeTitle = encodeURIComponent(videoTitle.value)
  window.location.href = `${import.meta.env.VITE_API_URL}/api/tiktok/download?url=${safeUrl}&title=${safeTitle}`
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
        <label class="input-label">Pega tu enlace de TikTok</label>

        <form @submit.prevent="fetchVideo">
          <input
            type="url"
            class="url-input"
            v-model="videoUrlInput"
            placeholder="https://www.tiktok.com/@usuario/video/123456789"
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
        <h1>Descargador de TikTok sin Marca de Agua</h1>
        <p>
          VideosNoWatermark es una forma sencilla de descargar videos de TikTok como archivos MP4
          sin marca de agua. Es gratuito y no requiere registro. Está optimizado para funcionar en
          todos los dispositivos, ya sea tu computadora, tableta o teléfono móvil.
        </p>

        <h3>Cómo descargar un video de TikTok</h3>
        <ol class="instructions-list">
          <li>
            <strong>1.</strong> Entra en la app de TikTok y busca el video que quieras descargar.
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
  /* El padding bottom extra es la clave para que la tarjeta se monte encima */
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
  color: #ff3b5c; /* Color estilo TikTok */
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
  margin: -80px auto 40px auto; /* Margen negativo para subirla encima del header oscuro */
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  width: 90%;
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

.format-selectors {
  display: flex;
  gap: 5px;
}

.format-btn {
  padding: 10px 20px;
  background: white;
  border: 1px solid #ddd;
  color: #333;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
}

.format-btn.active {
  background: #1a1a1a;
  color: white;
  border-color: #1a1a1a;
}

.convert-btn {
  background-color: #d9534f; /* Rojo estilo imagen */
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

.download-now-btn {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 15px 30px;
  font-size: 18px;
  font-weight: bold;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

.download-now-btn:hover {
  background-color: #218838;
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
