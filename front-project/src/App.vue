<template>
<div id="app">
    <h1>Personajes</h1>
    <ListaPernaje />
</div>
</template>

<script>
import ListaPernaje from './components/ListaPersonaje.vue'

export default {
    name: 'App',
    components: {
        ListaPernaje
    }
}
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
