<template>
  <div class="main-page-container">
    <!-- A classe 'blurred-content' NÃO está aqui -->
    <div class="content-area">
      <!-- Player Spotify -->
      <section class="spotify-section">
        <div class="spotify-frame-wrapper">
          <iframe
            style="border-radius:12px"
            src="https://open.spotify.com/embed/track/4JSROzfWqKccwZ68DX1aJe?utm_source=generator&theme=0"
            width="85%"
            height="152"
            frameBorder="0"
            allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture"
            loading="lazy">
          </iframe>
        </div>
      </section>

      <!-- Carrossel -->
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

      <!-- Timer -->
      <section class="timer-section">
        <h1>Juntos há</h1>
        <div class="timer-display">
          <p class="time-counter">{{ timeTogether }}</p>
        </div>
      </section>

      <!-- Texto/Mensagem -->
      <section class="text-section">
        <h1>Uma Mensagem para Você</h1>
        <div class="text-content">
          <p>
            O Lorem Ipsum tem vindo a ser o texto padrão usado por estas indústrias
            desde o ano de 1500, quando uma misturou os caracteres de um texto para
            criar um espécime de livro. Este texto não só sobreviveu 5 séculos, mas
            também o salto para a tipografia eletrônica, mantendo-se essencialmente inalterada...
          </p>
        </div>
      </section>
    </div>

    <!-- Overlay da Imagem Ampliada com Transição -->
    <Transition name="zoom-fade">
      <div v-if="enlargedImageIndex !== null" class="enlarged-image-overlay" @click.self="closeEnlargedImage">
        <!-- Container para a imagem e o texto com estilo polaroid -->
        <div class="polaroid-container">
          <img :src="carouselImages[enlargedImageIndex].src" :alt="carouselImages[enlargedImageIndex].alt" class="enlarged-image" />
          <!-- Área para o texto da polaroid -->
          <div class="polaroid-caption">
            <!-- Apenas o local/descrição agora -->
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

    const carouselImages = ref([
      // Removido o campo 'date'
      { src: '/photos/foto01.jpg', alt: 'Nossa Foto 1', location: 'Lovina 2025 - JP' },
      { src: '/photos/foto02.jpg', alt: 'Nossa Foto 2', location: 'Cicchetti - Pipa' },
      { src: '/photos/foto04.jpg', alt: 'Nossa Foto 4', location: 'Verão 2025 - Pirangi' },
      { src: '/photos/foto05.jpg', alt: 'Nossa Foto 5', location: 'Réveillon 2025 - Pirangi' },
      { src: '/photos/foto06.jpg', alt: 'Nossa Foto 6', location: 'Show de J&M - Bananeiras' },
      { src: '/photos/foto07.jpg', alt: 'Nossa Foto 7', location: 'Dom Vinicius - Natal' },
      { src: '/photos/foto08.jpg', alt: 'Nossa Foto 8', location: 'Carnaval 2024 - Pirangi' },
      { src: '/photos/foto09.jpg', alt: 'Nossa Foto 9', location: 'White 2024 - Pirangi' },
      // Continue preenchendo o campo 'location' para as outras fotos
    ]);

    const enlargedImageIndex = ref<number | null>(null);

    const enlargeImage = (index: number) => {
      enlargedImageIndex.value = index;
    };

    const closeEnlargedImage = () => {
      enlargedImageIndex.value = null;
    };

    // Add event listener for Escape key to close the modal
    const handleKeydown = (event: KeyboardEvent) => {
      if (event.key === 'Escape' && enlargedImageIndex.value !== null) {
        closeEnlargedImage();
      }
    };

    watch(enlargedImageIndex, (newValue) => {
        if (newValue !== null) {
            document.body.style.overflow = 'hidden'; // Prevent scrolling when modal is open
            window.addEventListener('keydown', handleKeydown);
        } else {
            document.body.style.overflow = ''; // Restore scrolling
            window.removeEventListener('keydown', handleKeydown);
        }
    });


    const updateTime = () => {
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

      // Replace the last comma with " e"
      const lastCommaIndex = timeString.lastIndexOf(',');
      if (lastCommaIndex > -1) {
        timeString = timeString.substring(0, lastCommaIndex) + ' e' + timeString.substring(lastCommaIndex + 1);
      }

      // Add ellipsis if seconds are included
      if (timeString.includes('segundo')) {
        timeString += '...';
      }

      timeTogether.value = timeString || 'Ainda não há tempo registrado';
    };

    onMounted(() => {
      updateTime();
      timerInterval = setInterval(updateTime, 1000) as any;
    });

    onUnmounted(() => {
      if (timerInterval) clearInterval(timerInterval);
      window.removeEventListener('keydown', handleKeydown); // Clean up keydown listener
      document.body.style.overflow = ''; // Ensure scrolling is restored on component unmount
    });

    return {
      timeTogether,
      carouselImages,
      enlargedImageIndex,
      enlargeImage,
      closeEnlargedImage,
    };
  },
});
</script>

<style scoped>
/* Importando a fonte Dancing Script */
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@400;700&display=swap');
/* Mantendo a importação de Quicksand, pois é usada para outros elementos */
@import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@400;600&display=swap');

/* ----------- ROOT ESTRUTURA ----------- */
.main-page-container {
  min-height: 100vh;
  width: 100vw;
  background: url('/images/background.jpg') no-repeat center center fixed;
  background-size: cover;
  position: relative;
  padding: 0;
  margin: 0;
  overflow-x: hidden;
}

.main-page-container::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(120deg, rgba(0,0,0,0.36) 38%, rgba(255,205,172,0.15) 100%);
  z-index: 1;
}

.content-area {
  position: relative;
  z-index: 2;
  background: rgba(255,255,255, 0.88);
  max-width: 880px;
  margin: 32px auto 48px auto;
  padding: 44px 52px 48px 52px;
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(158, 52, 41, 0.13);
  display: flex;
  flex-direction: column;
  gap: 44px;
}

section {
  background: transparent;
  padding: 0;
  border-radius: 0;
  box-shadow: none;
}

/* ----------- TITULOS E FONTE ----------- */
h1 {
  font-family: 'Dancing Script', cursive;
  color: #991938;
  text-align: center;
  font-size: 3.2em;
  margin-top: 0.3em;
  margin-bottom: 0.3em;
  letter-spacing: .9px;
  text-shadow: 1.5px 2px 24px #ffdcb8, 0 0 5px #e7bee333;
  line-height: 1.1;
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
  cursor: pointer; /* Indica que a imagem é clicável */
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

/* Carousel Controls */
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

/* Botões de navegação ("Setas") */
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
  font-family: 'Quicksand',sans-serif;
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
    background: rgba(0, 0, 0, 0.8); /* Fundo semi-transparente escuro */
    z-index: 100; /* Garante que fique acima de tudo */
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer; /* Indica que clicar no fundo fecha */
}

/* ----------- ESTILO POLAROID CONTAINER ----------- */
.polaroid-container {
    background-color: white; /* Fundo branco da polaroid */
    padding: 15px 15px 40px 15px; /* Padding para criar a borda (maior embaixo) */
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2); /* Sombra para dar profundidade */
    display: flex; /* Use flexbox para empilhar imagem e legenda */
    flex-direction: column;
    align-items: center; /* Centraliza os itens horizontalmente */
    max-width: 90%; /* Limita o tamanho máximo do container */
    max-height: 90%; /* Limita o tamanho máximo do container */
    box-sizing: border-box; /* Inclui o padding na largura e altura total */
    cursor: default; /* Restaura o cursor padrão dentro do container */
}

.enlarged-image {
    max-width: 100%; /* A imagem deve caber dentro do padding do container */
    max-height: calc(90vh - 80px); /* Ajusta a altura máxima considerando o espaço para a legenda e padding */
    object-fit: contain;
    /* Removido padding ou background aqui */
}

.polaroid-caption {
    width: 100%; /* A legenda ocupa a largura total da área de conteúdo do container */
    text-align: center;
    margin-top: 10px; /* Espaço entre a imagem e a legenda */
    font-family: 'Quicksand', sans-serif; /* Usa uma fonte legível */
    color: #555; /* Cor cinza escuro para o texto */
    font-size: 0.9em;
    line-height: 1.4;
}

.polaroid-caption p {
    margin: 0; /* Remove margens padrão dos parágrafos */
    padding: 0;
}


/* ----------- TRANSIÇÃO DE ZOOM E FADE ----------- */
/* Classes para a transição usando o nome "zoom-fade" */
.zoom-fade-enter-active,
.zoom-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.zoom-fade-enter-from,
.zoom-fade-leave-to {
  opacity: 0;
  transform: scale(0.8); /* Começa menor */
}

.zoom-fade-enter-to,
.zoom-fade-leave-from {
  opacity: 1;
  transform: scale(1); /* Termina no tamanho normal */
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
    background: rgba(255,255,255,0.97) !important;
  }
  h1 {
    font-size: 2.19em !important;
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
    position: absolute !important; /* Garantir posicionamento absoluto */
    top: 50% !important;
    transform: translateY(-50%) !important; /* Centralizar verticalmente */
    width: 31px !important;
    height: 31px !important;
    left: 8vw !important; /* Ajuste para mobile */
    right: 8vw !important; /* Ajuste para mobile */
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

  /* Ajuste do padding da polaroid em mobile, se necessário */
  .polaroid-container {
      padding: 10px 10px 30px 10px; /* Ajuste o padding para telas menores */
  }
   .enlarged-image {
       max-height: calc(80vh - 60px); /* Ajusta a altura máxima para mobile */
   }
   .polaroid-caption {
       font-size: 0.8em; /* Tamanho de fonte menor para mobile */
       margin-top: 8px; /* Ajusta a margem */
   }
}

/* Tablets (entre mobile e desktop) */
@media (max-width: 950px) and (min-width:601px){
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
}
</style>
