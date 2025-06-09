<template>
  <div class="main-page-container">
    <div class="content-area">
      <!-- Seção de Música do Spotify -->
      <section class="spotify-section">
        <!-- Código do embed do Spotify (sem o h2) -->
        <iframe
        style="border-radius:12px"
        src="https://open.spotify.com/embed/track/4JSROzfWqKccwZ68DX1aJe?utm_source=generator&theme=0"
        width="100%"
        height="152"
        frameBorder="0"
        allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture"
        loading="lazy">
        </iframe>
      </section>
      <!-- Seção de Carrossel de Fotos -->
      <section class="carousel-section">
        <h1>Nossos Momentos</h1>
        <!-- Componente do Carrossel -->
        <carousel :items-to-show="1" :autoplay="3000" :wrap-around="true">
          <slide v-for="(image, index) in carouselImages" :key="index">
            <img :src="image.src" :alt="image.alt" class="carousel-image">
          </slide>
          <template #addons>
            <navigation />
            <pagination />
          </template>
        </carousel>
      </section>
      <!-- Seção de Temporizador -->
      <section class="timer-section">
        <h1>Juntos há</h1>
        <div class="timer-display">
          <!-- Exibe o tempo detalhado aqui -->
          <p class="time-counter">{{ timeTogether }}</p>
        </div>
      </section>
      <!-- Seção de Texto -->
      <section class="text-section">
        <h1>Uma Mensagem para Você</h1>
        <div class="text-content">
          <p>
            O Lorem Ipsum tem vindo a ser o texto padrão usado por estas indústrias
            desde o ano de 1500, quando uma misturou os caracteres de um texto para
            criar um espécime de livro. Este texto não só sobreviveu 5 séculos, mas
            também o salto para a tipografia electrónica, mantendo-se essencialmente
            inalterada. Foi popularizada nos anos 60 com a disponibilização das
            folhas de Letraset, que continham passagens com Lorem Ipsum, e mais
            recentemente com os programas de publicação como o Aldus PageMaker que
            incluem versões do Lorem Ipsum.
          </p>
        </div>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
// Imports do Vue
import { defineComponent, ref, onMounted, onUnmounted } from 'vue';
// Imports de bibliotecas externas
// Importamos apenas intervalToDuration, pois differenceInDays não está sendo usado
import { intervalToDuration } from 'date-fns';
import { Carousel, Slide, Pagination, Navigation } from 'vue3-carousel';
import 'vue3-carousel/dist/carousel.css'; // Importe os estilos do carrossel

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
    const timeTogether = ref(''); // Ref para armazenar a string do tempo detalhado
    let timerInterval: number | undefined = undefined;

    // Caminho das imagens - verifique se estão em public/photos
    const carouselImages = ref([
      { src: '/photos/foto01.jpg', alt: 'Nossa Foto 1' }, // Adicionado alt text
      { src: '/photos/foto02.jpg', alt: 'Nossa Foto 2' }, // Adicionado alt text
      { src: '/photos/foto04.jpg', alt: 'Nossa Foto 4' }, // Adicionado alt text
      { src: '/photos/foto05.jpg', alt: 'Nossa Foto 5' }, // Adicionado alt text
      { src: '/photos/foto06.jpg', alt: 'Nossa Foto 6' }, // Adicionado alt text
      { src: '/photos/foto07.jpg', alt: 'Nossa Foto 7' }, // Adicionado alt text
      { src: '/photos/foto08.jpg', alt: 'Nossa Foto 8' }, // Adicionado alt text
      { src: '/photos/foto09.jpg', alt: 'Nossa Foto 9' }, // Adicionado alt text
    ]);

    const updateTime = () => {
      const now = new Date();
      // Calcula a duração entre a data de início e agora
      const duration = intervalToDuration({ start: startDate, end: now });

      // Formata a string de exibição
      const parts = [];
      if (duration.years) parts.push(`${duration.years} ano${duration.years > 1 ? 's' : ''}`);
      if (duration.months) parts.push(`${duration.months} mes${duration.months > 1 ? 'es' : ''}`);
      // Adiciona dias, horas, minutos e segundos apenas se houver anos ou meses,
      // ou se for o único componente de tempo restante.
      // Isso evita exibir "0 dias, 0 horas..." quando o tempo é menor que um dia.
      // Ou você pode simplesmente adicionar todos, como estava:
      if (duration.days) parts.push(`${duration.days} dia${duration.days > 1 ? 's' : ''}`);
      if (duration.hours) parts.push(`${duration.hours} hora${duration.hours > 1 ? 's' : ''}`);
      if (duration.minutes) parts.push(`${duration.minutes} minuto${duration.minutes > 1 ? 's' : ''}`);
      if (duration.seconds) parts.push(`${duration.seconds} segundo${duration.seconds > 1 ? 's' : ''}`);


      // Junta as partes com vírgula e "e" antes do último item
      let timeString = parts.join(', ');
      // Substitui a última vírgula antes do último item por " e "
      const lastCommaIndex = timeString.lastIndexOf(',');
      if (lastCommaIndex > -1) {
          timeString = timeString.substring(0, lastCommaIndex) + ' e' + timeString.substring(lastCommaIndex + 1);
      }

      timeTogether.value = timeString || 'Ainda não há tempo registrado'; // Adiciona um fallback caso o tempo seja 0
    };

    onMounted(() => {
      updateTime(); // Atualiza imediatamente ao montar
      // Configura o intervalo para atualizar a cada segundo
      timerInterval = setInterval(updateTime, 1000) as any; // Use 'any' para evitar erro de tipo com setInterval
    });

    onUnmounted(() => {
      // Limpa o intervalo ao desmontar o componente
      if (timerInterval) {
        clearInterval(timerInterval);
      }
    });

    return {
      timeTogether, // Retorna o ref do tempo detalhado
      carouselImages,
    };
  },
});
</script>

<style scoped>
.main-page-container {
  padding: 20px;
  max-width: 800px;
  margin: 0 auto;
  color: #333;
  /* Removido background-color: #f8f8f8; aqui para usar o fundo da WelcomePage */
  min-height: 100vh;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  /* Adicionado background-image para manter o fundo se WelcomePage não estiver presente */
  /* ou se você quiser um fundo diferente para esta página */
  /* background: url('/images/background.png') no-repeat center center fixed; */
  /* background-size: cover; */
}

.content-area {
  display: flex;
  flex-direction: column;
  gap: 40px;
  /* Adiciona um fundo branco ou claro para as seções de conteúdo */
  background-color: rgba(255, 255, 255, 0.9); /* Fundo semi-transparente branco */
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}


section {
  /* Removido background-color: #f8f8f8; aqui */
  padding: 25px;
  border-radius: 8px;
  /* Removido box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05); aqui */
  /* A sombra foi movida para .content-area */
}

h1 {
  color: #9c2b31;
  font-family: 'Sacramento', cursive;
  text-align: center;
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 1.8em;
}

/* Estilos para o iframe do Spotify */
.spotify-section iframe {
    display: block;
    margin: 0 auto;
    max-width: 100%;
}

/* Estilos para as imagens dentro do carrossel */
.carousel-image {
  max-width: 100%;
  height: auto;
  border-radius: 5px;
  display: block;
  margin: 0 auto;
}

/* Estilos para o display do timer */
.timer-display {
  text-align: center;
  padding: 15px;
  border-radius: 5px;
  color: #666;
}

/* Estilo para o contador de tempo detalhado */
.time-counter {
  font-size: 1.2em; /* Ajuste o tamanho da fonte se necessário */
  color: #333;
  font-weight: bold;
  margin-top: 10px;
}

.text-content p {
  line-height: 1.6;
  text-align: justify;
}

/* Estilos padrão para as setas de navegação do vue3-carousel */
/* Você pode ajustar se precisar, mas o padrão já funciona */

/* Responsividade */
@media (max-width: 600px) {
  .main-page-container {
    padding: 15px;
  }
  .content-area {
      padding: 15px;
  }
  section {
    padding: 20px;
  }
  h1 {
    font-size: 1.5em;
  }
  .time-counter { /* Ajuste também para o novo seletor */
    font-size: 1em;
  }
}
</style>
