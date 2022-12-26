<template>
  <div >
    <input type="text" v-model="room" />
    <button @click="joinRoom">join</button>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      room: "",
    };
  },
  methods: {
    joinRoom() {
      console.log(this.room);
      this.$router.push({ name: "room", params: { id: this.room } });
    },
  },
};
</script>
