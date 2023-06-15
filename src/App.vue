<template>
  <div id="app">
    <div class="wrapper">
      <HelloWorld></HelloWorld>
      <svg></svg>
    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';

export default {
  name: 'App',
  components: {
    HelloWorld
  }
};
</script>

<style>
svg {
  width: 100%;
  height: 100%;
  border: 1px solid white;
}

.wrapper {
  display: grid;
  gap: 10px;
  grid-template-columns: 1fr 1fr;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>