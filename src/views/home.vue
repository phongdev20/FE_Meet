<template>
  <div>
    <div class="header">
      <div>
        <h1 class="colorWhile boxLogo">MEET</h1>
      </div>
    </div>

    <div class="body">
      <p class="title">Cuộc họp video chất lượng. Giờ đây miễn phí cho tất cả mọi người.</p>
      <p class="subTitle">
        Chúng tôi đã thiết kế lại Google Meet — dịch vụ tổ chức cuộc họp kinh
        doanh với độ bảo mật cao — để cung cấp miễn phí cho mọi người. Hỗ trợ bạn kết nối với mọi người với mọi khoảng cách.
      </p>

      <div class="boxJoin">
        <input type="text" v-model="room" placeholder="Nhập số phòng" />
        <button @click="joinRoom">Bắt đầu</button>
      </div>

      <p class="subTitle colorRed">
        Nhập số phòng để bắt đầu một cuộc họp mới hoặc tham gia vào cuộc họp
      </p>
    </div>

    <div class="footer">
      <h1 class="colorWhile">Hân hạnh được phục vụ</h1>
      <p class="subTitle colorWhile">copyright ©: Nhóm 10</p>
    </div>
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

<style scoped>
button {
  font-weight: bold;
  height: 36px;
  border-radius: 24px;
  border-color: #1E90FF;
  background-color: #1E90FF;
  color: #ffffff;
  padding: 0px 32px;
}

input {
  border-radius: 24px;
  border-color: #1E90FF;
  padding: 0px 16px;
  height: 36px;
  min-width: 300px;
  margin-right: 16px;
}

.boxJoin {
  margin-top: 32px;
}

.header {
  height: 48px;
  background-color: #1E90FF;
}

.boxLogo {
  padding: 0px 16px;
}

.footer {
  min-height: 96px;
  background-color: #1E90FF;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  width: 100%;
  bottom: 0;
  flex-direction: column;
}

.body {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.title {
  margin-top: 96px;
  font-weight: bold;
  color: #0000FF;
  font-size: 36px;
  text-align: center;
  max-width: 800px;
}

.colorWhile {
  color: #ffffff;
}

.colorRed {
  color: #FF0000;
}

.subTitle {
  font-size: 18px;
  max-width: 1000px;
  text-align: center;
  margin-top: 16px;
}
</style>
