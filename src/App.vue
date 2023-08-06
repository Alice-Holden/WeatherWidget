<template>
  <WeatherWidget api-key="6e2498d51c77476afc6e11cb48b0e568"/>
</template>

<script>
import WeatherWidget from './components/WeatherWidget.vue'

export default {
  name: 'App',
  components: {
    WeatherWidget
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
}
html, body, div, span, h1, h2, h3, h4, h5, h6, p{
  margin: 0;
  padding: 0;
  border: 0;
  vertical-align: baseline;
}
</style>
