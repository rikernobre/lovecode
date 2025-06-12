<template>
  <div class="welcome-container">
    <div class="center-container">
      
      <h1>Oi, minha princesa!</h1>
      <p>Preparei uma surpresa para você.</p>

      <div class="envelope-wrapper" :class="{ 'opening': isOpening }">
        <div class="envelope-body">
          <div class="letter">
            <button @click="startOpeningAnimation" class="pulse-btn">Clique aqui</button>
          </div>
        </div>
        <div class="flap">
          <div class="heart-seal">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
              <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/>
            </svg>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script lang="ts">
// (O SCRIPT CONTINUA O MESMO)
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'WelcomePage',
  emits: ['proceed'],
  setup(_, { emit }) {
    const isOpening = ref(false);

    const emitProceed = () => {
      emit('proceed');
    };

    const startOpeningAnimation = () => {
      if (isOpening.value) return;
      isOpening.value = true;
      setTimeout(emitProceed, 1500);
    };

    return { 
      isOpening,
      startOpeningAnimation 
    };
  },
});
</script>

<style scoped>
/* --- Fontes Carregadas Localmente --- */
@font-face {
  font-family: 'Great Vibes';
  src: url('/fonts/GreatVibes-Regular.ttf') format('truetype');
  font-weight: normal;
}

@font-face {
  font-family: 'Quicksand';
  src: url('/fonts/Quicksand-Regular.ttf') format('truetype');
  font-weight: 500; /* Medium/Regular */
}

@font-face {
  font-family: 'Quicksand';
  src: url('/fonts/Quicksand-Bold.ttf') format('truetype');
  font-weight: 700; /* Bold */
}

/* O resto do seu CSS continua exatamente o mesmo */
.welcome-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(to bottom right, #ffcdac, #fff3e8);
  padding: 20px;
  box-sizing: border-box;
  perspective: 1200px;
}

.center-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 25px;
}

h1 {
  font-family: 'Great Vibes', cursive;
  color: #991938;
  font-size: 4em;
  text-shadow: 2px 2px 8px #ffe3d1;
  line-height: 1;
  margin: 0;
}

p {
  font-family: 'Quicksand', sans-serif;
  color: #a33e5a;
  font-size: 1.2em;
  font-weight: 500;
  margin: 0;
}

.envelope-wrapper {
  position: relative;
  width: 280px;
  height: 190px;
  transition: opacity 0.6s 0.9s, transform 0.6s 0.9s;
}

.envelope-wrapper.opening {
  opacity: 0;
  transform: translateY(-50px) scale(0.9);
}

.envelope-body {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 100%;
  background-color: #f7d9c4;
  border-radius: 6px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  overflow: hidden;
  z-index: 2;
}

.flap {
  position: absolute;
  top: 0;
  width: 100%;
  height: 55%;
  background-color: rgba(255, 205, 172, 0.85); 
  clip-path: polygon(0 0, 100% 0, 50% 100%);
  border-top-left-radius: 6px;
  border-top-right-radius: 6px;
  transform-origin: top;
  transition: transform 0.6s ease-in-out;
  z-index: 3;
  transform-style: preserve-3d;
  pointer-events: none; 
}

.envelope-wrapper.opening .flap {
  transform: rotateX(180deg);
}

.letter {
  position: absolute;
  bottom: 0;
  width: 90%;
  height: 90%;
  left: 5%;
  background: white;
  border-radius: 6px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  transition: transform 0.7s 0.2s ease-out;
  display: flex;
  justify-content: center;
  align-items: center;
}

.envelope-wrapper.opening .letter {
  transform: translateY(-60px);
}

.heart-seal {
  position: absolute;
  top: 45%;
  left: 50%;
  transform: translateX(-50%);
  width: 30px;
  height: 30px;
  color: #c0392b;
  backface-visibility: hidden;
  z-index: 4;
}

button, .pulse-btn {
  background-color: #991938;
  border: none;
  color: #fff3e8;
  font-family: 'Quicksand', sans-serif;
  font-weight: 700;
  font-size: 1em;
  padding: 10px 25px;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(153, 25, 56, 0.3);
  transition: all 0.3s ease;
  outline: none;
  margin-top: 80px;
  pointer-events: auto;
}

.pulse-btn:hover, .pulse-btn:focus {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 7px 25px rgba(153, 25, 56, 0.4);
}

.pulse-btn {
  animation: pulse 1.8s infinite;
}

@keyframes pulse {
  0% { box-shadow: 0 0 0 0 rgba(153, 25, 56, 0.4); }
  70% { box-shadow: 0 0 0 15px rgba(153, 25, 56, 0); }
  100% { box-shadow: 0 0 0 0 rgba(153, 25, 56, 0); }
}

@media (max-width: 600px) {
  .center-container {
    transform: scale(0.9);
  }
}
</style>