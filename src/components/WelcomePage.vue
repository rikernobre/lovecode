<template>
  <div class="welcome-container">
    <div class="content-wrapper">
      <h1>Oi, minha princesa!</h1>
      <p>Preparei uma surpresa para você.</p>
      <button @click="emitProceed" class="pulse-btn">Clique aqui</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'WelcomePage',
  emits: ['proceed'],
  setup(_, { emit }) {
    const emitProceed = () => {
      emit('proceed');
    };

    return { emitProceed };
  },
});
</script>

<style scoped>
/* Removendo a importação de Sacramento se não for mais usada */
/* @import url('https://fonts.googleapis.com/css2?family=Sacramento&display=swap'); */
/* Mantendo a importação de Dancing Script */
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@400;700&display=swap');
/* Removendo a importação de Quicksand se não for mais usada */
/* @import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@400;600&display=swap'); */


.welcome-container {
  min-height: 100vh;
  width: 100vw;
  background: url('/images/background.jpg') no-repeat center center fixed;
  background-size: cover;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.welcome-container::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(0,0,0,0.45) 30%, rgba(255,205,172,0.23) 100%);
  z-index: 1;
}

.content-wrapper {
  position: relative;
  z-index: 2;
  background: rgba(255,255,255,0.12);
  padding: 40px 32px 32px 32px;
  border-radius: 20px;
  box-shadow: 0 6px 28px rgba(0,0,0,0.16);
  backdrop-filter: blur(6px) saturate(130%);
  display: flex;
  flex-direction: column;
  align-items: center;
}

h1 {
  font-family: 'Dancing Script', cursive; /* Alterado para Dancing Script */
  color: #ffe3d1;
  font-size: 3.2em; /* Revertido para o tamanho original */
  margin: 0 0 12px 0;
  text-shadow: 3px 5px 10px rgba(102,0,5,0.16), 0 2px 5px rgba(0,0,0,0.3);
  letter-spacing: 1px;
  /* line-height: 1.2; Removido ajuste de line-height */
}

p {
  font-family: 'Dancing Script', cursive; /* Alterado para Dancing Script */
  color: #fff3e8;
  font-size: 1.2em; /* Revertido para o tamanho original */
  margin-bottom: 32px;
  text-shadow: 1px 1px 7px #66000530;
  letter-spacing: .4px;
  /* line-height: 1.5; Removido ajuste de line-height */
}

.pulse-btn, button {
  background: linear-gradient(90deg, #ffb4a2 15%, #ffe3d1 85%);
  border: none;
  color: #660005;
  font-family: 'Dancing Script', cursive; /* Alterado para Dancing Script */
  font-weight: 700; /* Dancing Script tem pesos 400 e 700 */
  font-size: 1.13em; /* Revertido para o tamanho original */
  padding: 14px 37px;
  border-radius: 40px;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(255,205,172,0.21), 0 1.5px 10px 0px #66000519;
  transition: box-shadow 0.23s, transform 0.16s, background 0.3s;
  outline: none;
}

.pulse-btn:hover, .pulse-btn:focus {
  background: linear-gradient(93deg, #ffcdac 0%, #fcd5ce 70%);
  box-shadow: 0 4px 25px #ffb4a275, 0 3px 9px 0 #66000525;
  transform: translateY(-2px) scale(1.047);
}

.pulse-btn {
  animation: pulse 1.6s infinite;
}

@keyframes pulse {
  0% { box-shadow: 0 0 0 0 #ffe3d121; }
  70% { box-shadow: 0 0 0 16px #ffcdac28;}
  100% { box-shadow: 0 0 0 0 #ffe3d100;}
}

@media (max-width: 600px) {
  .content-wrapper {
    padding: 24px 9vw 28px 9vw;
    border-radius: 15px;
  }

  h1 {
    font-size: 2.1em; /* Revertido para o tamanho original */
  }

  p {
    font-size: 1em; /* Revertido para o tamanho original */
  }

  button, .pulse-btn {
    font-size: 1em; /* Revertido para o tamanho original */
    padding: 12px 22px;
  }
}
</style>
