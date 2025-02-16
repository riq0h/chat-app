<template>
  <div style="background-color: lightcyan; padding: 20px">
    <h1 style="color: navy; font-family: Arial, sans-serif">VueChat - チャットルーム一覧</h1>
    <ul style="list-style-type: none; padding: 0">
      <li v-for="room in chatRooms" :key="room.id" style="margin-bottom: 10px">
        <router-link
          :to="`/rooms/${room.id}`"
          style="
            text-decoration: none;
            color: navy;
            background-color: lightgray;
            padding: 5px 10px;
            font-family: 'Courier New', monospace;
          "
          >{{ room.name }}</router-link
        >
      </li>
    </ul>
    <h3 style="color: navy; font-family: Arial, sans-serif">チャットルーム作成</h3>
    <input
      type="text"
      v-model="newRoomName"
      style="padding: 5px; font-family: 'Courier New', monospace"
    />
    <div style="margin-top: 10px">
      <button
        @click="createRoom"
        style="
          background-color: lightgray;
          color: navy;
          padding: 5px 10px;
          border: 1px solid navy;
          font-family: 'Courier New', monospace;
        "
      >
        作成
      </button>
    </div>
  </div>
</template>
<script>
import axios from 'axios'

export default {
  data() {
    return {
      chatRooms: [],
      newRoomName: '',
    }
  },
  created() {
    this.fetchChatRooms()
  },
  methods: {
    fetchChatRooms() {
      axios
        .get(`${import.meta.env.VITE_API_URL}/rooms`)
        .then((response) => {
          this.chatRooms = response.data
        })
        .catch((error) => {
          console.error(error)
        })
    },
    createRoom() {
      axios
        .post(`${import.meta.env.VITE_API_URL}/rooms`, {
          name: this.newRoomName,
        })
        .then((response) => {
          this.chatRooms.push(response.data)
          this.newRoomName = ''
        })
        .catch((error) => {
          console.error(error)
        })
    },
  },
}
</script>
