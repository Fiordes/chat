<template>
  <div class="container">
    <div class="connected" v-if="connected">
      <div class="form border rounded bg-red-900 px-10 py-10 flex flex-col">
        <input v-model="inputValue" type="text" />
        <button @click="sendMessage()" class="text-white">Send</button>
      </div>
      <div class="messages bg-gray-800 border rounded">
        <div v-for="mess in messages" :key="mess.id">
          <div class="bg-green-900" v-if="mess.event === 'connection'">
            <span>User {{ mess.username }} is connected</span>
          </div>
          <div class="bg-red-900" v-if="mess.event === 'message'">
            <span>{{ mess.username }} . {{ mess.message }}</span>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="err">
      <div class="form">
        <input v-model="userName" type="text" name="" placeholder="YOur Name" />
        <button @click="connect()">Login</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      messages: [],
      inputValue: "",
      connected: false,
      userName: "",
      socket: "",
    };
  },
  methods: {
    connect() {
      this.socket = new WebSocket("ws://localhost:5000");

      this.socket.onopen = () => {
        this.connected = true;

        const message = {
          event: "connection",
          username: this.userName,
          id: Date.now(),
        };

        this.socket.send(JSON.stringify(message));

        console.log("Connection succes");
      };

      this.socket.onmessage = (event) => {
        const message = JSON.parse(event.data);
        this.messages = [message, ...this.messages];
      };

      this.socket.onclose = () => {
        console.log("Socked is closed");
      };
      this.socket.onerror = () => {
        console.log("Socked is closed");
      };
    },
    sendMessage() {
      const message = {
        username: this.userName,
        message: this.inputValue,
        id: Date.now(),
        event: "message",
      };

      this.socket.send(JSON.stringify(message));
      this.inputValue = "";
    },
  },
  mounted() {
    this.socket = new WebSocket("ws://localhost:5000");
  },
};
</script>

<style></style>
