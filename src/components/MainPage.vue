<template>
  <div class="main-page-container">
    <div class="content-area">
      <section class="audio-player-section">
        <div class="audio-player-container">
          <audio ref="audioPlayer">
            <source src="/audio/nossa-musica.mp3" type="audio/mpeg">
            Seu navegador não suporta o elemento de áudio.
          </audio>

          <div class="player-controls-area">
            <div class="time-display">
              <span class="current-time">{{ formatTime(currentTime) }}</span>
              <span class="separator">/</span>
              <span class="total-duration">{{ formatTime(duration) }}</span>
            </div>

            <div class="progress-bar-container" @click="seek">
              <div class="progress-bar" :style="{ width: progress + '%' }"></div>
            </div>

            <div class="playback-buttons">
              <button @click="toggleRepeat" :class="{ active: isRepeating }" class="control-button repeat-button" title="Repetir">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M7 7h10v3l4-4-4-4v3H5v6h2V7zm10 10H7v-3l-4 4 4 4v-3h12v-6h-2v3z"/></svg>
              </button>
              <button @click="skipBackward" class="control-button skip-backward-button" title="Retroceder 10s">
                 <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M12 5V1L7 6l5 5V7c3.31 0 6 2.69 6 6s-2.69 6-6 6-6-2.69-6-6H4c0 4.42 3.58 8 8 8s8-3.58 8-8-3.58-8-8-8z"/></svg>
              </button>
              <button @click="togglePlayPause" class="control-button play-pause-button" :title="isPlaying ? 'Pause' : 'Play'">
                <svg v-if="!isPlaying" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M8 5v14l11-7z"/></svg>
                <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z"/></svg>
              </button>
              <button @click="skipForward" class="control-button skip-forward-button" title="Avançar 10s">
                 <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M12 19V15l-5 5 5 5v-4c3.31 0 6-2.69 6-6s-2.69-6-6-6-6 2.69-6 6H4c0-4.42 3.58-8 8-8s8 3.58 8 8-3.58 8-8 8z"/></svg>
              </button>
               <button @click="toggleShuffle" :class="{ active: isShuffling }" class="control-button shuffle-button" title="Aleatório">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24px" height="24px"><path d="M0 0h24v24H0z" fill="none"/><path d="M10.59 9.17L5.41 4 4 5.41l5.17 5.17 1.42-1.41zM14.5 4l2.04 2.04L4 18.59 5.41 20 18.59 7.46 20 9V4h-5.5zm.5 10H12v1.59l5-5V10h3v6z"/></svg>
              </button>
            </div>
          </div>
        </div>
      </section>

      <section class="carousel-section">
        <h1>Nossos Momentos</h1>
        <carousel :items-to-show="1" :autoplay="3500" :wrap-around="true">
          <slide v-for="(image, index) in carouselImages" :key="`img${index}`">
            <div class="carousel-img-wrapper" @click="enlargeImage(index)">
              <img :src="image.src" :alt="image.alt" class="carousel-image" />
            </div>
          </slide>
          <template #addons>
            <navigation />
            <pagination />
          </template>
        </carousel>
      </section>

      <section class="timer-section">
        <h1>Juntos há</h1>
        <div class="timer-display">
          <p class="time-counter">{{ timeTogether }}</p>
        </div>
      </section>

      <section class="text-section">
        <h1>Uma Mensagem para Você</h1>
        <div class="text-content">
          <p>
            O Lorem Ipsum é um texto modelo da indústria tipográfica e de impressão.
            O Lorem Ipsum tem vindo a ser o texto padrão usado por estas indústrias desde
            o ano de 1500, quando uma misturou os caracteres de um texto para criar um espécime
            de livro. Este texto não só sobreviveu 5 séculos, mas também o salto para a tipografia
            electrónica, mantendo-se essencialmente inalterada. Foi popularizada nos anos 60 com
            a disponibilização das folhas de Letraset, que continham passagens com Lorem Ipsum, e
            mais recentemente com os programas de publicação como o Aldus PageMaker que incluem
            versões do Lorem Ipsum.
          </p>
        </div>
      </section>
    </div>

    <Transition name="zoom-fade">
      <div v-if="enlargedImageIndex !== null" class="enlarged-image-overlay" @click.self="closeEnlargedImage">
        <div class="polaroid-container">
          <img :src="carouselImages[enlargedImageIndex].src" :alt="carouselImages[enlargedImageIndex].alt" class="enlarged-image" />
          <div class="polaroid-caption">
            <p>{{ carouselImages[enlargedImageIndex].location }}</p>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onUnmounted, watch } from 'vue';
import { intervalToDuration } from 'date-fns';
import { Carousel, Slide, Pagination, Navigation } from 'vue3-carousel';
import 'vue3-carousel/dist/carousel.css';

export default defineComponent({
  name: 'MainPage',
  components: {
    Carousel,
    Slide,
    Pagination,
    Navigation,
  },
  setup() {
    const startDate = new Date('2024-02-12T00:00:00');
    const timeTogether = ref('');
    let timerInterval: number | undefined = undefined;

    // Audio Player Refs and State
    const audioPlayer = ref<HTMLAudioElement | null>(null);
    const isPlaying = ref(false);
    const currentTime = ref(0);
    const duration = ref(0);
    const progress = ref(0);
    const isRepeating = ref(false);
    const isShuffling = ref(false);
    
    // --- LÓGICA DO PLAYER REFEITA ---

    // O botão agora apenas alterna o estado. A lógica de play/pause foi centralizada no 'watch'.
    const togglePlayPause = () => {
      isPlaying.value = !isPlaying.value;
    };

    // 'watch' atua como o ÚNICO controlador do áudio.
    // Ele observa a variável 'isPlaying' e comanda o player.
    watch(isPlaying, (newIsPlayingValue) => {
      if (!audioPlayer.value) return;

      if (newIsPlayingValue) {
        // Se o estado mudou para "tocando", executa o play.
        const playPromise = audioPlayer.value.play();
        if (playPromise !== undefined) {
          playPromise.catch(error => {
            console.error("Erro ao tentar tocar a música (autoplay pode estar bloqueado):", error);
            // Se o play falhar (ex: bloqueio do navegador), reverte o estado para 'false'.
            isPlaying.value = false;
          });
        }
      } else {
        // Se o estado mudou para "pausado", executa o pause.
        audioPlayer.value.pause();
      }
    });

    // Sincroniza o estado 'isPlaying' com os eventos nativos do player
    const handlePlay = () => {
      if (!isPlaying.value) isPlaying.value = true;
    };

    const handlePause = () => {
      if (isPlaying.value) isPlaying.value = false;
    };

    const formatTime = (seconds: number): string => {
      if (isNaN(seconds) || seconds === Infinity) {
        return '00:00';
      }
      const minutes = Math.floor(seconds / 60);
      const remainingSeconds = Math.floor(seconds % 60);
      const formattedMinutes = String(minutes).padStart(2, '0');
      const formattedSeconds = String(remainingSeconds).padStart(2, '0');
      return `${formattedMinutes}:${formattedSeconds}`;
    };

    const handleEnded = () => {
        isPlaying.value = false;
        if (!isRepeating.value && audioPlayer.value) {
            audioPlayer.value.currentTime = 0;
        }
    };

    const handleTimeUpdate = () => {
      if (audioPlayer.value) {
        currentTime.value = audioPlayer.value.currentTime;
        duration.value = audioPlayer.value.duration;
        progress.value = (currentTime.value / duration.value) * 100 || 0;
      }
    };

    const handleLoadedMetadata = () => {
        if (audioPlayer.value) {
            duration.value = audioPlayer.value.duration;
        }
    };

    const seek = (event: MouseEvent) => {
      if (audioPlayer.value) {
        const progressBarContainer = event.currentTarget as HTMLElement;
        const clickX = event.clientX - progressBarContainer.getBoundingClientRect().left;
        const containerWidth = progressBarContainer.offsetWidth;
        const seekTime = (clickX / containerWidth) * duration.value;
        audioPlayer.value.currentTime = seekTime;
      }
    };

    const skipBackward = () => {
      if (audioPlayer.value) {
        audioPlayer.value.currentTime = Math.max(0, audioPlayer.value.currentTime - 10);
      }
    };

    const skipForward = () => {
      if (audioPlayer.value) {
        audioPlayer.value.currentTime = Math.min(duration.value, audioPlayer.value.currentTime + 10);
      }
    };

    const toggleRepeat = () => {
      isRepeating.value = !isRepeating.value;
      if (audioPlayer.value) {
          audioPlayer.value.loop = isRepeating.value;
      }
    };

    const toggleShuffle = () => {
      isShuffling.value = !isShuffling.value;
      console.log(`Shuffle is now ${isShuffling.value ? 'on' : 'off'}`);
    };

    const carouselImages = ref([
      { src: '/photos/foto01.jpg', alt: 'Nossa Foto 1', location: 'Lovina 2025 - JP' },
      { src: '/photos/foto02.jpg', alt: 'Nossa Foto 2', location: 'Cicchetti - Pipa' },
      { src: '/photos/foto04.jpg', alt: 'Nossa Foto 4', location: 'Verão 2025 - Pirangi' },
      { src: '/photos/foto05.jpg', alt: 'Nossa Foto 5', location: 'Réveillon 2025 - Pirangi' },
      { src: '/photos/foto06.jpg', alt: 'Nossa Foto 6', location: 'Show de J&M - Bananeiras' },
      { src: '/photos/foto07.jpg', alt: 'Nossa Foto 7', location: 'Dom Vinicius - Natal' },
      { src: '/photos/foto08.jpg', alt: 'Nossa Foto 8', location: 'Carnaval 2024 - Pirangi' },
      { src: '/photos/foto09.jpg', alt: 'Nossa Foto 9', location: 'White 2024 - Pirangi' },
    ]);

    const enlargedImageIndex = ref<number | null>(null);

    const enlargeImage = (index: number) => {
      enlargedImageIndex.value = index;
    };

    const closeEnlargedImage = () => {
      enlargedImageIndex.value = null;
    };

    const handleKeydown = (event: KeyboardEvent) => {
      if (event.key === 'Escape' && enlargedImageIndex.value !== null) {
        closeEnlargedImage();
      }
    };

    watch(enlargedImageIndex, (newValue) => {
        if (newValue !== null) {
            document.body.style.overflow = 'hidden';
            window.addEventListener('keydown', handleKeydown);
        } else {
            document.body.style.overflow = '';
            window.removeEventListener('keydown', handleKeydown);
        }
    });

    const updateTime = () => {
      // (código do timer continua o mesmo)
      const now = new Date(); 
      const duration = intervalToDuration({ start: startDate, end: now });
      const parts = [];
      if (duration.years) parts.push(`${duration.years} ano${duration.years > 1 ? 's' : ''}`);
      if (duration.months) parts.push(`${duration.months} mes${duration.months > 1 ? 'es' : ''}`);
      if (duration.days) parts.push(`${duration.days} dia${duration.days > 1 ? 's' : ''}`);
      if (duration.hours) parts.push(`${duration.hours} hora${duration.hours > 1 ? 's' : ''}`);
      if (duration.minutes) parts.push(`${duration.minutes} minuto${duration.minutes > 1 ? 's' : ''}`);
      if (duration.seconds) parts.push(`${duration.seconds} segundo${duration.seconds > 1 ? 's' : ''}`);
      let timeString = parts.join(', ');
      const lastCommaIndex = timeString.lastIndexOf(',');
      if (lastCommaIndex > -1) {
        timeString = timeString.substring(0, lastCommaIndex) + ' e' + timeString.substring(lastCommaIndex + 1);
      }
      if (timeString.includes('segundo')) {
        timeString += '...';
      }
      timeTogether.value = timeString || 'Ainda não há tempo registrado';
    };

    onMounted(() => {
      updateTime();
      timerInterval = setInterval(updateTime, 1000) as any;

      if (audioPlayer.value) {
        audioPlayer.value.addEventListener('play', handlePlay);
        audioPlayer.value.addEventListener('pause', handlePause);
        audioPlayer.value.addEventListener('ended', handleEnded);
        audioPlayer.value.addEventListener('timeupdate', handleTimeUpdate);
        audioPlayer.value.addEventListener('loadedmetadata', handleLoadedMetadata);

        // Apenas define o estado inicial para tocar. O 'watch' fará o resto.
        isPlaying.value = true;
      }
    });

    onUnmounted(() => {
      if (timerInterval) clearInterval(timerInterval);
      window.removeEventListener('keydown', handleKeydown);
      document.body.style.overflow = '';

      if (audioPlayer.value) {
        audioPlayer.value.removeEventListener('play', handlePlay);
        audioPlayer.value.removeEventListener('pause', handlePause);
        audioPlayer.value.removeEventListener('ended', handleEnded);
        audioPlayer.value.removeEventListener('timeupdate', handleTimeUpdate);
        audioPlayer.value.removeEventListener('loadedmetadata', handleLoadedMetadata);
      }
    });

    return {
      timeTogether,
      carouselImages,
      enlargedImageIndex,
      enlargeImage,
      closeEnlargedImage,
      audioPlayer,
      isPlaying,
      togglePlayPause,
      currentTime,
      duration,
      progress,
      formatTime,
      seek,
      skipBackward,
      skipForward,
      isRepeating,
      toggleRepeat,
      isShuffling,
      toggleShuffle
    };
  },
});
</script>

<style scoped>
/* Adicionando a importação da nova fonte no topo */
@import url('https://fonts.googleapis.com/css2?family=Great+Vibes&display=swap');

/* Estilos existentes */
.main-page-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  min-height: 100vh;
  background: linear-gradient(to bottom right, #ffcdac, #fff3e8);
  padding: 20px;
  box-sizing: border-box;
}
.content-area {
  background: rgba(255, 255, 255, 0.9);
  padding: 30px;
  border-radius: 15px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  max-width: 800px;
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 30px;
}

/* --- ALTERAÇÃO DA FONTE --- */
h1 {
  font-family: 'Great Vibes', cursive; /* Nova fonte romântica */
  text-align: center;
  color: #991938;
  margin-bottom: 1em;
  font-size: 3.2em; /* Ajuste no tamanho para a nova fonte */
  text-shadow: 2px 2px 8px #ffe3d1;
}

section {
  margin-bottom: 20px;
}
/* ----------- AUDIO PLAYER ----------- */
.audio-player-section {
  text-align: center;
}
.audio-player-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: linear-gradient(92deg, #ffcdacc9 50%, #fff3e8ee 100%);
  padding: 20px;
  border-radius: 13px;
  box-shadow: 0 0px 12px #ffb4a240, 0 2px 11px #99193816;
  max-width: 500px;
  margin: 0 auto;
}
.audio-player-container audio {
  display: none;
}
.player-controls-area {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.time-display {
  font-family: 'Quicksand', sans-serif;
  font-size: 0.9em;
  color: #991938;
  margin-bottom: 8px;
}
.separator {
  margin: 0 5px;
}
.progress-bar-container {
  width: 95%;
  height: 8px;
  background-color: #ffe3d1;
  border-radius: 4px;
  margin-bottom: 15px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.progress-bar {
  height: 100%;
  background-color: #991938;
  border-radius: 4px;
  transition: width 0.1s linear;
}
.playback-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
}
.control-button {
  background: none;
  border: none;
  color: #991938;
  cursor: pointer;
  padding: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: color 0.2s ease, transform 0.1s ease;
}
.control-button svg {
    width: 24px;
    height: 24px;
    fill: currentColor;
}
.control-button:hover {
  color: #ffcdac;
  transform: scale(1.1);
}
.control-button.active svg {
    fill: #ffcdac;
}
.play-pause-button svg {
    width: 36px;
    height: 36px;
}
/* ----------- CARROSSEL ----------- */
.carousel-section {
  margin-bottom: 0;
}
.carousel-img-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  cursor: pointer;
}
.carousel-image {
  width: 480px;
  max-width: 80vw;
  min-width: 300px;
  max-height: 360px;
  object-fit: cover;
  border-radius: 16px;
  display: block;
  margin: 0 auto;
  box-shadow: 0 3px 22px #99193823, 0 1.5px 6px #ffdcb830;
  border: 2px solid #ffcdacc2;
  background: #ffffffcd;
  transition: box-shadow .2s;
}
.carousel__pagination-button--active {
  background: #991938 !important;
  border-color: #ffdcb8 !important;
}
.carousel__icon {
  color: #991938 !important;
}
.carousel__pagination-button {
  width: 10px;
  height: 10px;
}
.carousel__prev,
.carousel__next {
  position: absolute !important;
  top: 50% !important;
  transform: translateY(-50%) !important;
  background: #ffe3d1e8 !important;
  border-radius: 50% !important;
  width: 38px !important;
  height: 38px !important;
  box-shadow: 0 2px 10px #ffdcb870 !important;
  opacity: 0.96 !important;
  border: none !important;
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
  z-index: 5;
}
.carousel__prev {
  left: 14px !important;
}
.carousel__next {
  right: 14px !important;
}
.carousel__icon {
  font-size: 1.65em !important;
}
/* ----------- TIMER ----------- */
.timer-display {
  text-align: center;
  padding: 19px 0;
  border-radius: 13px;
  background: linear-gradient(92deg, #ffcdacc9 50%, #fff3e8ee 100%);
  color: #991938;
  font-family: 'Quicksand', sans-serif;
  font-weight: 500;
  font-size: 1.16em;
  box-shadow: 0 0px 12px #ffb4a240, 0 2px 11px #99193816;
  margin-bottom: 5px;
}
.time-counter {
  font-size: 1.19em;
  font-weight: 700;
  color: #991938;
  margin: 0;
  text-shadow: 1px 2px 8px #fff7e8b6;
  letter-spacing: .8px;
  font-family: "Quicksand", 'Segoe UI', Arial, sans-serif;
}
/* ----------- TEXTO ----------- */
.text-content p {
  font-family: 'Quicksand', sans-serif;
  line-height: 1.7;
  color: #991938;
  text-align: justify;
  font-size: 1.08em;
  background: #ffe3d1da;
  padding: 17px 24px 12px 24px;
  border-radius: 11px;
  box-shadow: 0 1.5px 8px #ffcdac23;
  margin: 1em;
}
/* ----------- OVERLAY DA IMAGEM AMPLIADA ----------- */
.enlarged-image-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  z-index: 100;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
/* ----------- ESTILO POLAROID CONTAINER ----------- */
.polaroid-container {
  background-color: white;
  padding: 15px 15px 40px 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 90%;
  max-height: 90%;
  box-sizing: border-box;
  cursor: default;
}
.enlarged-image {
  max-width: 100%;
  max-height: calc(90vh - 80px);
  object-fit: contain;
}
.polaroid-caption {
  width: 100%;
  text-align: center;
  margin-top: 10px;
  font-family: 'Quicksand', sans-serif;
  color: #555;
  font-size: 0.9em;
  line-height: 1.4;
}
.polaroid-caption p {
  margin: 0;
  padding: 0;
}
/* ----------- TRANSIÇÃO DE ZOOM E FADE ----------- */
.zoom-fade-enter-active,
.zoom-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.zoom-fade-enter-from,
.zoom-fade-leave-to {
  opacity: 0;
  transform: scale(0.8);
}
.zoom-fade-enter-to,
.zoom-fade-leave-from {
  opacity: 1;
  transform: scale(1);
}
/* ========== RESPONSIVIDADE =========== */
@media (max-width: 600px) {
  .main-page-container,
  .content-area {
    padding: 0 !important;
    margin: 0 !important;
    border-radius: 0 !important;
    max-width: 100vw !important;
  }
  .content-area {
    padding-top: 8px !important;
    padding-bottom: 14px !important;
    gap: 18px !important;
    background: rgba(255, 255, 255, 0.97) !important;
  }
  h1 {
    font-size: 2.8em !important; /* Ajuste responsivo */
    margin-top: 0.4em !important;
    margin-bottom: 0.5em !important;
    letter-spacing: 1.1px !important;
    text-shadow: 2px 3px 18px #ffe3d1, 0 0 2px #9919382f;
  }
  .carousel-image {
    width: 86vw !important;
    min-width: 86vw !important;
    max-width: 86vw !important;
    min-height: 33vw;
    max-height: 43vw;
    border-radius: 6vw;
  }
  .carousel__prev,
  .carousel__next {
    position: absolute !important;
    top: 50% !important;
    transform: translateY(-50%) !important;
    width: 31px !important;
    height: 31px !important;
    left: 8vw !important;
    right: 8vw !important;
  }
  .carousel__prev {
    left: 8vw !important;
  }
  .carousel__next {
    right: 8vw !important;
  }
  .carousel__icon {
    font-size: 1.18em !important;
  }
  .timer-display {
    font-size: 1em !important;
    padding: 12px 0 !important;
    max-width: 94vw !important;
    border-radius: 8px !important;
    margin: 0 3vw 5px 3vw;
  }
  .text-content p {
    font-size: 0.96em !important;
    padding: 10px 5vw 8px 5vw;
    border-radius: 6px !important;
  }
  .polaroid-container {
    padding: 10px 10px 30px 10px;
  }
  .enlarged-image {
    max-height: calc(80vh - 60px);
  }
  .polaroid-caption {
    font-size: 0.8em;
    margin-top: 8px;
  }
  /* Ajustes para o player de áudio em mobile */
  .audio-player-container {
    padding: 15px;
    max-width: 85vw; /* Aumentei um pouco para melhor aproveitamento */
    margin: 0 auto;
  }
  .playback-buttons {
      gap: 10px;
  }
  .control-button svg {
      width: 20px;
      height: 20px;
  }
  .play-pause-button svg {
      width: 30px;
      height: 30px;
  }
  .time-display {
      font-size: 0.8em;
  }
  .progress-bar-container {
      height: 6px;
      width: 90%;
  }
}
/* Tablets (entre mobile e desktop) */
@media (max-width: 950px) and (min-width:601px) {
  .polaroid-container {
    padding: 12px 12px 35px 12px;
  }
  .enlarged-image {
    max-height: calc(85vh - 70px);
  }
  .polaroid-caption {
    font-size: 0.85em;
    margin-top: 9px;
  }
    /* Ajustes para o player de áudio em tablet */
  .audio-player-container {
    max-width: 600px;
  }
    .playback-buttons {
      gap: 12px;
  }
    .control-button svg {
      width: 22px;
      height: 22px;
  }
  .play-pause-button svg {
      width: 32px;
      height: 32px;
  }
}
</style>