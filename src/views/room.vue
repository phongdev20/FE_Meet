<template>
  <div>
    <div v-for="(item, index) in message" :key="index">{{ item }}</div>
    <input type="text" v-model="input" />
    <button @click="handleSendMessage">Send</button>
    <button @click="leave">leave</button>
    <button @click="stream('getUserMedia')">stream cam</button>
    <button @click="stream('getDisplayMedia')">stream Screen</button>
    <div class="video">
      <div class="video-grid" ref="video-grid"></div>
    </div>
  </div>
</template>