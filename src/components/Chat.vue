// Install dependencies: npm install vue socket.io-client

<template>
  <div class="chat-container">
    <div class="messages">
      <div v-for="(message, index) in messages" :key="index" class="message">
        {{ message }}
      </div>
    </div>
    <input v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type a message..." />
    <button @click="sendMessage">Send</button>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import { io } from 'socket.io-client';

export default {
  setup() {
    const socket = io('http://localhost:3000');
    const messages = ref([]);
    const newMessage = ref('');

    onMounted(() => {
      socket.on('chat message', (msg) => {
        messages.value.push(msg);
      });
    });

    const sendMessage = () => {
      if (newMessage.value.trim() !== '') {
        socket.emit('chat message', newMessage.value);
        newMessage.value = '';
      }
    };

    return { messages, newMessage, sendMessage };
  }
};
</script>

<style>
.chat-container {
  max-width: 400px;
  margin: auto;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.messages {
  border: 1px solid #ccc;
  padding: 10px;
  height: 200px;
  overflow-y: auto;
  background-color: black;
}
.message {
  /* background: #f1f1f1; */
  padding: 5px;
  margin: 5px 0;
  border-radius: 5px;
  color:white
}
input {
  padding: 10px;
  width: 100%;
}
button {
  padding: 10px;
  background: blue;
  color: white;
  border: none;
  cursor: pointer;
}
</style>
