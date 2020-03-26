<template>
  <div id="app">
    <CaptureImage />
  </div>
</template>

<script>
import CaptureImage from './components/CaptureImage.vue';

export default {
  name: 'App',

  components: {
    CaptureImage,
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100vw;
  min-height: 100vh;
  text-align: center;
  color: #333333;
  overflow-y: hidden;
}
</style>
