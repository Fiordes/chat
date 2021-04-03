<template>
  <div class="container">
    <div class="form border rounded bg-red-900 px-10 py-10 flex flex-col">
      <input v-model="inputValue" type="text" />
      <button @click="sendMessage()" class="text-white">Send</button>
    </div>
    <div class="messages bg-green-800 border rounded">
      <div v-for="mess in messages" :key="mess.id" class="message">
        {{ mess.message }}
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
      inputValue: ""
    };
  },
  methods: {
    async sendMessage() {
      await axios.post("http://localhost:5050/new-messages", {
        message: this.inputValue,
        id: Date.now()
      });
    },
    async subcribe() {
      try {
        const { data } = await axios.get("http://localhost:5050/get-messages");
        this.messages = [data, ...this.messages];
        await this.subcribe();
      } catch (error) {
        setTimeout(() => {
          this.subcribe();
        }, 500);
      }
    }
  },
  mounted() {
    this.subcribe();
  }
};
</script>

<style></style>
