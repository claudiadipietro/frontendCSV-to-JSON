<template>
  <DropZone />
</template>

<script>
import DropZone from './components/DropZone.vue'

export default {
  name: 'App',
  components: {
    DropZone
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
body{
  background-color: rgb(207, 207, 207);
}
</style>
