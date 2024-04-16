<template>
  <GestioProyectos msg="Welcome to Your Vue.js App"/>
</template>

<script>
import GestioProyectos from './components/GestioProyectos.vue'
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  name: 'App',
  components: {
    GestioProyectos
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
