<template>
  <div style="background-color: lightcyan; padding: 20px">
    <h1 style="color: navy; font-family: Arial, sans-serif">チャットルーム {{ this.roomId }}</h1>

    <ul style="list-style-type: none; padding: 0">
      <li
        v-for="message in messages"
        :key="message.id"
        style="background-color: lightgray; padding: 10px; margin-bottom: 10px"
      >
        <strong style="color: navy">{{ message.sender_name }}></strong> {{ message.content }}
      </li>
    </ul>
    <form @submit.prevent="sendMessage" style="background-color: lightgray; padding: 20px">
      <div>
        <h3 style="color: navy">名前</h3>
        <input
          type="text"
          v-model="senderName"
          placeholder="名前を入力"
          required
          style="width: 100%; padding: 5px; border: 1px solid gray"
        />
      </div>
      <div>
        <h3 style="color: navy">メッセージ</h3>
        <input
          type="text"
          v-model="newMessageContent"
          placeholder="メッセージを入力"
          required
          style="width: 100%; padding: 5px; border: 1px solid gray"
        />
      </div>
      <div style="text-align: center; margin-top: 10px">
        <button
          type="submit"
          style="
            background-color: lightgray;
            color: navy;
            padding: 5px 10px;
            border: 1px solid navy;
            font-family: 'Courier New', monospace;
          "
        >
          送信
        </button>
      </div>
    </form>
  </div>
</template>
<script>
import axios from 'axios'
import { inject } from 'vue'
export default {
  props: ['roomId'],
  data() {
    return {
      roomName: '',
      messages: [],
      senderName: '',
      newMessageContent: '',
    }
  },
  setup() {
    const cable = inject('cable')
    return { cable }
  },
  created() {
    this.fetchMessages()
    this.createSubscription()
  },
  methods: {
    // 追加
    createSubscription() {
      this.subscription = this.cable.subscriptions.create(
        { channel: 'RoomChannel', room_id: this.roomId },
        {
          received: (message) => {
            console.log(message)
            this.messages.push(message)
          },
        },
      )
    },
    fetchMessages() {
      axios
        .get(`${import.meta.env.VITE_API_URL}/rooms/${this.roomId}/messages`)
        .then((response) => {
          this.messages = response.data
        })
        .catch((error) => {
          console.error(error)
        })
    },
    sendMessage() {
      axios
        .post(`${import.meta.env.VITE_API_URL}/rooms/${this.roomId}/messages`, {
          content: this.newMessageContent,
          sender_name: this.senderName,
        })
        .then(() => {
          this.newMessageContent = ''
        })
        .catch((error) => {
          console.error(error)
        })
    },
  },
}
</script>
