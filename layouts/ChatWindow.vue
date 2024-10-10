<template>
  <div class="chat-window">
    <div v-if="selectedChat">
      <h2>{{ selectedChat.name }}</h2>
      <div class="messages">
        <div
          v-for="(message, index) in selectedChat.messages"
          :key="index"
          :class="{
            'message-sent': isSentByUser(message),
            'message-received': !isSentByUser(message),
          }"
        >
          <p>{{ message.text }}</p>
        </div>
      </div>
      <div class="input-area">
        <input
          v-model="newMessage"
          @keyup.enter="sendMessage"
          placeholder="请输入内容..."
        />
        <button @click="sendMessage">发送</button>
      </div>
    </div>
    <div v-else>
      <p>请选择一个聊天对象</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    selectedChat: Object,
  },
  data() {
    return {
      newMessage: "",
      ws: null,
    };
  },
  methods: {
    isSentByUser(message) {
      return message.isUser;
    },
    sendMessage() {
      if (this.newMessage.trim() !== "") {
        const message = {
          id: null, // id 为空
          from: "消息来源用户名",
          to: this.selectedChat.name,
          content: this.newMessage,
        };
        this.selectedChat.messages.push({
          text: this.newMessage,
          isUser: true,
        });
        this.$emit("messageSent", this.selectedChat.id, {
          text: this.newMessage,
          isUser: true,
        });
        this.ws.send(JSON.stringify(message));
        this.newMessage = "";
      }
    },
    connectWebSocket() {
      this.ws = new WebSocket("ws://localhost:8081", ["your-jwt-token"]);

      this.ws.onmessage = (event) => {
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

      this.ws.onopen = () => {
        console.log("WebSocket connection established");
      };

      this.ws.onclose = () => {
        console.log("WebSocket connection closed");
      };

      this.ws.onerror = (error) => {
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
.chat-window {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 20px;
  background-color: #f7f7f7;
  position: relative;
  height: 100vh; /* 确保 chat-window 占满整个视口高度 */
}
.messages {
  flex: 1;
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
  overflow-y: auto; /* 启用滚动 */
  background-color: #fff;
  max-height: calc(
    100vh - 160px
  ); /* 确保 messages 不会超过 chat-window 的高度 */
}
.message-sent {
  text-align: right;
  margin: 10px 0;
}
.message-received {
  text-align: left;
  margin: 10px 0;
}
.message-sent p {
  display: inline-block;
  padding: 10px;
  background-color: #dcf8c6;
  border-radius: 10px;
}
.message-received p {
  display: inline-block;
  padding: 10px;
  background-color: #fff;
  border-radius: 10px;
  border: 1px solid #ccc;
}
.input-area {
  display: flex;
  border-top: 1px solid #ccc;
  padding: 10px;
  background-color: #fff;
  position: sticky; /* 固定在底部 */
  bottom: 0;
}
.input-area input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
}
.input-area button {
  padding: 10px 20px;
  border: none;
  background-color: #4caf50;
  color: #fff;
  border-radius: 5px;
  cursor: pointer;
}
.input-area button:hover {
  background-color: #45a049;
}
</style>
