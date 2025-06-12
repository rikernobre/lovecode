<template>
  <div id="app">
    <WelcomePage v-if="showWelcomePage" @proceed="handleProceed" />

    <MainPage v-else />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import WelcomePage from './components/WelcomePage.vue';
import MainPage from './components/MainPage.vue'; 

export default defineComponent({
  name: 'App',
  components: {
    WelcomePage,
    MainPage,
  },
  setup() {
    const showWelcomePage = ref(true);

    const handleProceed = () => {
      showWelcomePage.value = false;
    };

    return {
      showWelcomePage,
      handleProceed,
    };
  },
});
</script>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  padding: 0;
  width: 100%;
  min-height: 100vh;
  overflow-x: hidden;
}

</style>
