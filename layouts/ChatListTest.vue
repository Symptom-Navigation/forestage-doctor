<template>
  <div class="chat-list">
    <ul>
      <li
        v-for="(chat, index) in chats"
        :key="index"
        @click="selectChat(index)"
      >
        <img :src="chat.avatar" alt="avatar" class="avatar" />
        <div class="chat-info">
          <h3>{{ chat.name }}</h3>
          <p>{{ chat.lastMessage }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script lang="ts" setup>
import { defineProps } from "vue";

interface Chat {
  avatar: string;
  name: string;
  lastMessage: string;
}

const props = defineProps<{
  chats: Chat[];
  selectChat: (index: number) => void;
}>();
</script>

<style scoped>
.chat-list {
  width: 250px;
  border-right: 1px solid #ccc;
  background-color: #f7f7f7;
}
ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
li {
  display: flex;
  align-items: center;
  padding: 10px;
  cursor: pointer;
  border-bottom: 1px solid #e6e6e6;
}
li:hover {
  background-color: #e6e6e6;
}
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
}
.chat-info {
  flex: 1;
}
.chat-info h3 {
  margin: 0;
  font-size: 16px;
  color: #333;
}
.chat-info p {
  margin: 5px 0 0;
  font-size: 14px;
  color: #999;
}
</style>
