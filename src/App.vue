<template>
  <div >
    <router-view></router-view>
    <input type="text" v-model="room" />
    <button @click="joinRoom">join</button>
  </div>
</template>
<script>
import { io } from "socket.io-client";
import { Peer } from "peerjs";
const socket = io("http://localhost:3200", {
  reconnectionDelayMax: 10000,
  transports: ["websocket", "polling", "flashsocket"]
});
const peer = new Peer(undefined, {
  host: "localhost",
  port: "3300",
  path: "/peer"
});

export default {
  name: "App",
  data() {
    return {
      input: "",
      message: [],
      room: "",
      peer_id: "",
      my_video_stream: "",
      is_streaming: false,
      is_screen_streaming: false,
      enable_video: true,
      enable_audio: true,
    };
  },
  created() {
    this.getStream();
    socket.on("joined", peer_ids => {
      console.log("joined", peer_ids);
      for (const id of peer_ids) {
        if (id !== this.peer_id) {
          this.connectToNewUser(id, this.my_video_stream);
          this.connectToNewUser(id, this.my_screen_stream);
        }
      }
    });

    socket.on("left", peer_id => {
      document.getElementById(peer_id).remove();
    });

    socket.on("new_message", data => {
      this.message.push(data);
    });

    peer.on("open", id => {
      this.peer_id = id;
    });

    peer.on("call", call => {
      console.log("call", call, this.is_screen_streaming, this.is_streaming);
      this.is_streaming && call.answer(this.my_video_stream);
      this.is_screen_streaming && call.answer(this.my_screen_stream);
      const video = document.createElement("video");
      video.setAttribute("id", call.peer);
      call.on("stream", userStream => {
        console.log("peer call on stream", userStream);
        this.addVideoStream(video, userStream);
      });
    });
  },
  methods: {
    handleSendMessage() {
      socket.emit("send_message", {
        message: this.input,
        room: this.room
      });
    },
    joinRoom() {
      socket.emit("join", {
        room: this.room,
        peer_id: this.peer_id
      });
    },
    leave() {
      socket.emit("leave", this.room);
    },
    async getStream() {
      this.my_video_stream = await navigator.mediaDevices["getUserMedia"]({
        video: this.enable_video,
        audio: this.enable_audio
      });
      const tracks = this.my_video_stream.getTracks();
      tracks.forEach(track => {
        track.stop();
      });
    },
    async stream(type) {
      const video = document.createElement("video");
      if (type === "getUserMedia") {
        if (this.is_streaming) {
          this.is_streaming = false;
          socket.emit("stop-sharing", {
            room: this.room,
            peer_id: this.peer_id
          });
          const tracks = this.my_video_stream.getTracks();
          tracks.forEach(track => track.stop());
          return document.getElementById("my-video").remove();
        } else {
          var stream = await navigator.mediaDevices["getUserMedia"]({
            video: true,
            audio: true
          });
          this.my_video_stream = stream;
          this.is_streaming = true;

          video.setAttribute("id", "my-video");
        }
      } else if (type === "getDisplayMedia") {
        if (this.is_screen_streaming) {
          this.is_screen_streaming = false;
          socket.emit("stop-sharing", {
            room: this.room,
            peer_id: this.peer_id
          });
          const tracks = this.my_screen_stream.getTracks();
          tracks.forEach(track => track.stop());
          return document.getElementById("my-screen").remove();
        } else {
          var stream = await navigator.mediaDevices["getDisplayMedia"]({
            video: true,
            audio: true
          });
          this.my_screen_stream = stream;
          this.is_screen_streaming = true;
          video.setAttribute("id", "my-screen");
        }
      }
      this.addVideoStream(video, stream);
      socket.emit("start-sharing", {
        room: this.room,
        peer_id: this.peer_id
      });
    },
    addVideoStream(video, stream) {
      video.muted = true;
      video.srcObject = stream;
      video.addEventListener("loadedmetadata", () => {
        video.play();
      });
      this.$refs["video-grid"].appendChild(video);
      stream.oninactive = () => {
        video.remove();
      };
    },
    async connectToNewUser(user_id, stream) {
      const call = peer.call(user_id, stream);

      const video = document.createElement("video");
      video.setAttribute("id", user_id);
      call.on("stream", userStream => {
        console.log("stream", userStream);
        this.addVideoStream(video, userStream);
      });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.video {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 80vh;
}

.video-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, (minmax(300px, 1fr)));
  grid-auto-rows: 300px;
}

#my-screen {
  width: 300px;
  height: 300px;
  object-fit: cover;
}

#my-video {
  width: 300px;
  height: 300px;
  object-fit: cover;
}
</style>
