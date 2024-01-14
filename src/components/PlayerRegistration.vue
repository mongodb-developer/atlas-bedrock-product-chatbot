<template>
  <div class="header">
      <img src="../assets/banner-inv.png" class="header-img" alt="Logo" />
    </div>
    <div class="registration">
      <h2>Player Registration</h2>
      <input type="text" v-model="playerName" placeholder="Player Name" />
      <input type="email" v-model="email" placeholder="Email" />
      <button @click="registerPlayer">Register to start</button>
    </div>
    <img src="../assets/dribble-dots.gif" class="dribble" alt="Logo" />
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        playerName: '',
        email: ''
      };
    },
    methods: {
      async registerPlayer() {
        try {
          if (!this.validateRegistration(this.playerName, this.email)) {
            alert('Please enter a valid player name and email address');
            return;
          }
          console.log('registering player');
          console.log('VUE_APP_BASE_APP_SERVICE_URL', process.env.VUE_APP_BASE_APP_SERVICE_URL);
          console.log('VUE_APP_REGISTER_ENDPOINT', process.env.VUE_APP_REGISTER_ENDPOINT);
          const response = await axios.post(
            `${process.env.VUE_APP_BASE_APP_SERVICE_URL}/endpoint/${process.env.VUE_APP_REGISTER_ENDPOINT}`,
            { player: this.playerName, email: this.email }
          );
          this.$emit('setPlayer', { name: this.playerName, email: this.email });
          console.log('Registration successful:', response.data);
        } catch (error) {
          console.error('Registration failed:', error);
        }
      },
      validateRegistration(playerName, email) {
        return playerName && email && email.includes('@');
      }
    }
  };
  </script>
  
  <style scoped>
  .registration {
    display: flex;
    flex-direction: column;
    max-width: 300px;
    margin: 50px auto;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 10px;
    background-color: #f9f9f9;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }

  .header {
    background-color: #f5f5f5;
    padding: 20px;
    margin-bottom: 20px;
    display: flex;
    justify-content: center;
  }
  
  .header-img {
    max-width: 100%;
    max-height: 300px;
  }

  .registration h2 {
    text-align: center;
    color: #333;
    margin-bottom: 20px;
  }
  
  .registration input[type="text"],
  .registration input[type="email"] {
    padding: 10px;
    margin-bottom: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
    outline: none;
  }
  
  .registration input[type="text"]:focus,
  .registration input[type="email"]:focus {
    border-color: #007bff;
  }
  
  .registration button {
    padding: 10px;
    border: none;
    border-radius: 5px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  .dribble{
    visibility: hidden;
  }
  .registration button:hover {
    background-color: #0056b3;
  }
  </style>
  