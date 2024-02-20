<template>
  <div id="app">
    <MainChat :player="username"/>
  </div>
</template>

<script>
import MainChat from './components/MainChat.vue'

export default {
  name: 'App',
  components: {
    MainChat
  },
  data() {
    return {
      username: {"name" : prompt("Please insert your name...")}
    }
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
