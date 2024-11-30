<template>
  <h1>Roll your dice</h1>
  <button @click="rollDice(4)">Roll 1d4</button>
  <button @click="rollDice(6)">Roll 1d6</button>
  <button @click="rollDice(8)">Roll 1d8</button>
  <button @click="rollDice(12)">Roll 1d12</button>
  <button @click="rollDice(20)">Roll 1d20</button>
  <button @click="rollDice(100)">Roll 1d100</button>
  <div>
    {{ lastDice }}
  </div>
  <button class="card" @click="displayDiceModal">
    <div>Click to roll dice</div>
  </button>
  <dialog class="dice-modal" :open="displayModal">
    <p>Let's roll your dice</p>

    <select autofocus>
      <option value="dog">Dog</option>
      <option value="cat">Cat</option>
      <option value="hamster">Hamster</option>
      <option value="parrot">Parrot</option>
      <option value="spider">Spider</option>
      <option value="goldfish">Goldfish</option>
    </select>
    <button>Roll</button>
  </dialog>

  <h1>Chat</h1>
  <button @click="joinChat">join chat</button>

  message array !!!! {{ messageArray }}
  <div v-for="(message, i) in messageArray" :key="i">
    {{ i }} : Last message: {{ message }}
  </div>
  <div v-if="displayModal" class="hider" />
</template>

<script setup lang="ts">
  import { io } from 'socket.io-client';
  import { ref } from 'vue';

  const lastDice = ref();
  const displayModal = ref();
  const messageArray = ref<any>([]);
  const connected = ref(false);
  const socketioInstance = ref();

  const rollDice = (num: number) => {
    lastDice.value = Math.floor(Math.random() * num + 1);

    messageArray.value.push(lastDice.value);
    socketioInstance.value.emit('message', lastDice.value);
  };
  const displayDiceModal = () => {
    displayModal.value = true;
  };
  const joinChat = () => {
    if (!connected.value) {
      socketioInstance.value = io('http://localhost:3000');
      connected.value = true;
    }
    socketioInstance.value.on('message:received', (data: string) => {
      messageArray.value.push(data);
    });
  };
</script>

<style scoped>
  .card {
    background: unset;
    border: unset;
    width: 150px;
    height: 150px;
  }

  .dice-modal {
    z-index: 2;
    background-color: aquamarine;
  }

  .hider {
    z-index: 1;
    top: 0;
    left: 0;
    position: absolute;
    height: 100%;
    width: 100%;
    background: #99999999;
  }
</style>
