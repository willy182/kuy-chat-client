// Install dependencies: npm install vue socket.io-client

<template>
  <div class="chat-container">
    <!-- Header Chat -->
    <div class="chat-header">
      <h2>Real-Time Chat</h2>
      <p>Online</p>
    </div>

    <!-- Daftar Pesan -->
    <div class="chat-messages">
      <div
        v-for="(message, index) in messages"
        :key="index"
        :class="['message', message.sender === 'me' ? 'sent' : 'received']"
      >
        <div class="message-content">
          <span class="message-sender">{{ message.sender }}</span>
          <p>{{ message.text }}</p>
          <span class="message-time">{{ message.time }}</span>
        </div>
      </div>
    </div>

    <!-- Input Pesan -->
    <div class="chat-input">
      <input
        v-model="newMessage"
        @keyup.enter="sendMessage"
        placeholder="Type a message..."
      />
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { io } from 'socket.io-client';

export default defineComponent({
  name: 'ChatWindow',
  setup() {
    const messages = ref<{ sender: string; text: string; time: string }[]>([]);
    const newMessage = ref('');
    const socket = io('http://localhost:3000');

    const sendMessage = () => {
      if (newMessage.value.trim() !== '') {
        const message = {
          sender: '',
          text: newMessage.value,
          time: new Date().toLocaleTimeString(),
        };
        socket.emit('chat message', message);
        newMessage.value = '';
      }
    };

    onMounted(() => {
      socket.on('chat message', (msg) => {
        messages.value.push(msg);
      });
    });

    return {
      messages,
      newMessage,
      sendMessage,
    };
  },
});
</script>

<style scoped>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  max-width: 600px;
  margin: 0 auto;
  border: 1px solid #e0e0e0;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  background-color: #f9f9f9;
}

.chat-header {
  padding: 15px;
  background-color: #6200ea;
  color: white;
  text-align: center;
}

.chat-header h2 {
  margin: 0;
  font-size: 1.5rem;
}

.chat-header p {
  margin: 5px 0 0;
  font-size: 0.9rem;
  color: #e0e0e0;
}

.chat-messages {
  flex: 1;
  padding: 15px;
  overflow-y: auto;
  background-color: #ffffff;
}

.message {
  display: flex;
  margin-bottom: 15px;
}

.message.sent {
  justify-content: flex-end;
}

.message.received {
  justify-content: flex-start;
}

.message-content {
  max-width: 70%;
  padding: 10px 15px;
  border-radius: 10px;
  position: relative;
}

.message.sent .message-content {
  background-color: #6200ea;
  color: white;
}

.message.received .message-content {
  background-color: #e0e0e0;
  color: #333;
}

.message-sender {
  font-size: 0.8rem;
  font-weight: bold;
  margin-bottom: 5px;
  display: block;
}

.message-time {
  font-size: 0.7rem;
  color: #888;
  display: block;
  margin-top: 5px;
}

.chat-input {
  display: flex;
  padding: 10px;
  background-color: #ffffff;
  border-top: 1px solid #e0e0e0;
}

.chat-input input {
  flex: 1;
  padding: 10px;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  margin-right: 10px;
  font-size: 1rem;
}

.chat-input button {
  padding: 10px 20px;
  background-color: #6200ea;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
}

.chat-input button:hover {
  background-color: #3700b3;
}
</style>
