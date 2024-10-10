<template>
  <NuxtLayout name="menu">
    <div class="chat-container">
      <ChatList :chats="chats" :selectChat="selectChat" />
      <ChatWindow
        :selectedChat="selectedChat"
        @messageSent="updateLastMessage"
      />
    </div>
  </NuxtLayout>
</template>

<script>
import ChatList from "../layouts/ChatListTest.vue";
import ChatWindow from "../layouts/ChatWindow.vue";

export default {
  components: {
    ChatList,
    ChatWindow,
  },
  data() {
    return {
      chats: [
        {
          id: 5,
          name: "Test",
          avatar: "https://img.picui.cn/free/2024/09/23/66f096676bb08.png", // 默认头像
          lastMessage: "这是最后一条消息",
          messages: [
            { text: "你好！", isUser: false },
            { text: "你好，Test！", isUser: true },
            { text: "今天过得怎么样？", isUser: false },
            { text: "还不错，你呢？", isUser: true },
            { text: "我也很好，谢谢！", isUser: false },
            { text: "有任何问题随时告诉我。", isUser: true },
            { text: "好的，谢谢！", isUser: false },
            { text: "这是最后一条消息", isUser: true },
          ],
        },
      ],
      selectedChat: null,
    };
  },
  methods: {
    selectChat(index) {
      this.selectedChat = this.chats[index];
    },
    updateLastMessage(chatIndex, message) {
      this.chats[chatIndex].lastMessage = message.text;
    },
    connectWebSocket() {
      const ws = new WebSocket("ws://localhost:8081", ["your-jwt-token"]);

      ws.onmessage = (event) => {
        const message = JSON.parse(event.data);
        const existingChat = this.chats.find(
          (chat) => chat.name === message.from
        );

        if (existingChat) {
          existingChat.lastMessage = message.content;
          existingChat.messages.push({ text: message.content, isUser: false });
        } else {
          this.chats.push({
            id: message.id,
            name: message.from,
            avatar: "https://img.picui.cn/free/2024/09/23/66f096676bb08.png", // 默认头像
            lastMessage: message.content,
            messages: [{ text: message.content, isUser: false }],
          });
        }
      };

      ws.onopen = () => {
        console.log("WebSocket connection established");
      };

      ws.onclose = () => {
        console.log("WebSocket connection closed");
      };

      ws.onerror = (error) => {
        console.error("WebSocket error:", error);
      };
    },
  },
  mounted() {
    this.connectWebSocket();
  },
};
</script>

<style scoped>
.chat-container {
  display: flex;
  height: 100vh;
}
</style>
