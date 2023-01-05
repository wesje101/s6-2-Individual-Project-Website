<template>
  <div>
    <h1>Browse chatrooms</h1>
    <ul>
      <h2>Create a chatroom</h2>
      <input v-model="ChatName" type="text" placeholder="Chat name" />
      <button @click="postChat">Add Chat</button>
      <h2>Chats:</h2>
      <li v-for="Chat in Chats" v-bind:key="Chat._roomName">
        <RouterLink :to="{name: 'Chat', params: {chatId: Chat._id}}">
          Name: {{Chat._roomName}}
        </RouterLink>
        <p>Chat name: {{ Chat._roomName }}</p>
        <ul v-for="Participant in Chat._participants" v-bind:key="Participant._name">
          <p>{{Participant._name}}</p>
        </ul>
        <button @click="joinChat(Chat)">Join Chat</button>
        <button @click="leaveChat(Chat)">Leave Chat</button>

      </li>
      <h2>User:</h2>
      <input v-model="id" type="number" placeholder="id" />
      <input v-model="username" type="text" placeholder="name" />
    </ul>
  </div>
</template>

<script>
import {useRoute} from "vue-router";
export default {
  data() {
    return {
      Chats: [],
      ChatName: "",
      ChatDescription: "",
      id: 0,
      username: "",
    };
  },
  name: "Chat.vue",
  methods: {
    getChats() {
      fetch("http://localhost:8101/api/chatrooms").then((response) => {
        if (response.ok) {
          return response.json().then((data) => {
            this.Chats = data;
          });
        }
      });
    },
    postChat() {
      fetch(
        "http://localhost:8101/api/CreateChatRoom?roomName=" + this.ChatName,
        {
          method: "POST",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
        }
      ).then((response) => {
        if (response.ok) {
          return response.json().then((data) => {
            this.Chats.push(data);
          });
        }
      });
    },
    joinChat(Chat) {
      fetch(
        "http://localhost:8101/api/JoinChatRoomByName?roomName=" +
          Chat._roomName,
        {
          method: "POST",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            _id: this.id,
            _name: this.username,
          }),
        }
      ).then((response) => {
        if (response.ok) {
          this.getChats();
        }
      });
    },
    leaveChat(Chat) {
      fetch(
          "http://localhost:8101/api/LeaveChatRoomByName?roomName=" +
          Chat._roomName,
          {
            method: "POST",
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              _id: this.id,
              _name: this.username,
            }),
          }
      ).then((response) => {
        if (response.ok) {
          this.getChats();
        }
      });
    },
  },
  mounted() {
    this.getChats();
  },
};
</script>

<style scoped></style>
