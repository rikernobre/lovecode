<template>
  <div class="welcome-container">
    <div class="content-wrapper">
      <h1>Olá, meu amor!</h1>
      <p>Preparei uma surpresa para você.</p>
      <button @click="emitProceed">Clique aqui</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'WelcomePage',
  emits: ['proceed'], // Declara que este componente pode emitir um evento chamado 'proceed'
  setup(_, { emit }) {
    const emitProceed = () => {
      emit('proceed'); // Emite o evento 'proceed' quando o botão é clicado
    };

    return {
      emitProceed,
    };
  },
});
</script>

<style scoped>
.welcome-container {
  display: flex;
  justify-content: center; /* Centraliza horizontalmente */
  align-items: center; /* Centraliza verticalmente */
  min-height: 100vh; /* Garante que o container ocupe a altura total da viewport */
  width: 100%;
  background: url('/background.jpg') no-repeat center center fixed; /* Exemplo de background com imagem */
  /* Ou use uma cor sólida: background-color: #ffc0cb; */
  background-size: cover; /* Garante que a imagem cubra toda a área */
  color: white; /* Cor do texto para contrastar com o background */
  text-align: center; /* Centraliza o texto */
  position: relative; /* Necessário para sobrepor o conteúdo se a imagem for muito escura */
  z-index: 1;
}

/* Adiciona uma sobreposição escura para melhorar a legibilidade do texto, se necessário */
.welcome-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.4); /* Cor preta com 40% de opacidade */
  z-index: -1; /* Fica atrás do conteúdo */
}


.content-wrapper {
    /* Se precisar de algum padding ou margin extra para o conteúdo */
    padding: 20px;
}

h1 {
  font-size: 2.5em; /* Tamanho do título */
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5); /* Sombra para destacar o texto */
}

p {
    font-size: 1.2em;
    margin-bottom: 20px;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5); /* Sombra para destacar o texto */
}

button {
  padding: 12px 25px;
  font-size: 1.1em;
  cursor: pointer;
  border: none;
  border-radius: 5px;
  background-color: #ff69b4; /* Cor de botão rosa */
  color: white;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #ff85c1; /* Cor rosa mais clara ao passar o mouse */
}

/* Estilos para responsividade em telas menores (opcional, mas recomendado) */
@media (max-width: 600px) {
  h1 {
    font-size: 1.8em;
  }

  p {
    font-size: 1em;
  }

  button {
    padding: 10px 20px;
    font-size: 1em;
  }
}
</style>
