<template>
  <div id="app">
    <PlayerRegistration v-if="!player" @setPlayer="setPlayer"/>
    <MainChat v-if="player" :player="player"/>
  </div>
</template>
<script>
import MainChat from './components/MainChat.vue'
import PlayerRegistration from './components/PlayerRegistration.vue'
export default {
  components: {
    PlayerRegistration,
    MainChat
  },
  data() {
    return {
      player: null
    };
  },
  methods: {
    setPlayer(playerData) {
      this.player = playerData;
    }
  }
};
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
