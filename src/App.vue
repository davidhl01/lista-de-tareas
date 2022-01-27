<template>
  <cabecera></cabecera>
  <tareas></tareas>
</template>

<script>
import cabecera from './components/cabecera.vue'
import tareas from './components/tareas.vue'

export default {
  name: 'App',
  components: {
    tareas,cabecera
  }
}
</script>

<style>
body{
	font-family: 'Baloo Tamma 2', cursive;
  background-image: url("assets/Fondo.jpg");
}
</style>
