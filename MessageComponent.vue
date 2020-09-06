<template>
  <div class="chat-content tabto active">
    <div class="messages custom-scroll active" id="chating">
      <!-- Mesajlaşmada üstte gösterilen bilgi kısmı -->
      <div class="contact-details">
        <div class="row">
          <div class="col-7">
            <div class="media left">
              <div class="media-left mr-3 d-flex align-items-center">
                <div class="profile" :class="friend.online ? 'online' : 'offline'">
                  <img class="bg-img" :src="uploads_src + friend.image" :alt="friend.name" />
                </div>
                <h3 class="ml-3">{{friend.name}}</h3>
              </div>
              <div class="media-body"></div>
              <div class="media-right">
                <ul>
                  <li></li>
                  <li></li>
                  <li>
                    <a @click="backToHome" class="icon-btn btn-light button-effect mobile-sidebar" href="#">
                      <chevron-left-icon size="1.5x" class="custom-class"></chevron-left-icon>
                    </a>
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <div class="col">
            <ul class="calls text-right">
              <li>
                <a
                  class="px-3 icon-btn-call icon-btn btn-light button-effect"
                  href="#"
                  data-toggle="modal"
                  data-target="#videocall"
                  @click.prevent="onJoin"
                >
                  <span class="pr-2">Görüntülü Görüşme</span>
                  <video-icon size="1.5x" class="custom-class"></video-icon>
                </a>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <!-- Üstte gösterilen bilgi kısmı END -->
      <!-- Mesajlar -->
      <div class="contact-chat">
        <ul class="chatappend">
          <li v-for="chat in this.chats" :class="chat.type == 0 ? 'sent': 'replies'" :key="chat.id">
            <ul class="msg-box">
              <li class="msg-setting-main">
                <h5>{{chat.message}}</h5>
              </li>
              <h6>{{chat.send_at}}</h6>
            </ul>
          </li>
        </ul>
      </div>
      <!-- Mesajlar End -->
    </div>
    <form @submit.prevent="send" class="message-input">
      <div class="wrap emojis-main">
        <div class="dot-btn dot-primary mr-3">
          <a class="icon-btn btn-outline-primary button-effect toggle-emoji">
            <smile-icon size="1.5x" class="custom-class"></smile-icon>
          </a>
        </div>
        <input
          :disabled="session.block"
          v-model="message"
          maxlength="1000"
          class="setemoj"
          id="setemoj"
          type="text"
          placeholder="Mesajınızı yazın (en fazla 1000 karakter)"
        />
        <button @click.prevent="send" class="submit icon-btn btn-primary" id="send-msg">
          <send-icon size="1.5x" class="custom-class"></send-icon>
        </button>

        <!-- Emojiler -->
        <div class="emojis-contain">
          <div class="emojis-sub-contain custom-scroll">
            <ul>
              <li>&#128512;</li>
              <li>&#128513;</li>
              <li>&#128514;</li>
              <li>&#128515;</li>
              <li>&#128516;</li>
              <li>&#128517;</li>
              <li>&#128518;</li>
              <li>&#128519;</li>
              <li>&#128520;</li>
              <li>&#128521;</li>
              <li>&#128522;</li>
              <li>&#128523;</li>
              <li>&#128524;</li>
              <li>&#128525;</li>
              <li>&#128526;</li>
              <li>&#128527;</li>
              <li>&#128528;</li>
              <li>&#128529;</li>
              <li>&#128530;</li>
              <li>&#128531;</li>
              <li>&#128532;</li>
              <li>&#128533;</li>
              <li>&#128534;</li>
              <li>&#128535;</li>
              <li>&#128536;</li>
              <li>&#128537;</li>
              <li>&#128538;</li>
              <li>&#128539;</li>
              <li>&#128540;</li>
              <li>&#128541;</li>
              <li>&#128542;</li>
              <li>&#128543;</li>
              <li>&#128544;</li>
              <li>&#128545;</li>
              <li>&#128546;</li>
              <li>&#128547;</li>
              <li>&#128549;</li>
              <li>&#128550;</li>
              <li>&#128551;</li>
              <li>&#128552;</li>
              <li>&#128553;</li>
              <li>&#128554;</li>
              <li>&#128555;</li>
              <li>&#128557;</li>
              <li>&#128558;</li>
              <li>&#128559;</li>
              <li>&#128560;</li>
              <li>&#128561;</li>
              <li>&#128562;</li>
              <li>&#128563;</li>
              <li>&#128564;</li>
              <li>&#128565;</li>
              <li>&#128566;</li>
              <li>&#128567;</li>
              <li>&#128568;</li>
              <li>&#128569;</li>
              <li>&#128570;</li>
              <li>&#128571;</li>
              <li>&#128572;</li>
              <li>&#128573;</li>
              <li>&#128574;</li>
              <li>&#128576;</li>
              <li>&#128579;</li>
            </ul>
          </div>
        </div>
      </div>
    </form>

    <!-- Video Chat Container -->
    <div class="viddiolog modal fade" id="videocall" tabindex="-1" role="dialog" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-body">
          <div class="videocall call-modal">
            <vue-webrtc
              ref="webrtc"
              width="100%"
              v-on:joined-room="this.setVideoSettings"
              v-on:left-room="this.logEvent"
              v-on:open-room="this.setVideoSettings"
              v-on:share-started="this.logEvent"
              v-on:share-stopped="this.logEvent"
              :roomId="this.roomId + this.session.id.toString()"
              @error="this.onError"
              class="h-100 w-100"
            />
            <!-- <img class="bg-img" :src="chat_src + 'images/avtar/big/videocall_bg.jpg'" alt="Avatar" />
            <div class="small-image">
              <img class="bg-img" :src="chat_src + 'images/avtar/big/videocall.jpg'" alt="Avatar" />
            </div>-->
            <div class="media videocall-details">
              <div class="usersprof">
                <div class="profile">
                  <img class="bg-img" :src="uploads_src + friend.image" alt="Avatar" />
                </div>
              </div>
              <div class="media-body">
                <h5>{{friend.name}}</h5>
              </div>
              <div id="basicUsage">00:00:00</div>
              <div class="zoomcontent">
                <a
                  class="text-dark"
                  href="#!"
                  onclick="javascript:toggleFullScreen()"
                  data-tippy-content="Zoom Screen"
                >
                  <img :src="chat_src + 'images/logo/maximize.svg'" alt="zoom screen" />
                </a>
              </div>
            </div>
            <div class="center-con text-center">
              <ul>
                <li>
                  <a
                    class="icon-btn btn-light button-effect pause"
                    href="#"
                    data-tippy-content="Hold"
                    @click.prevent="hideCamera"
                  >
                    <i class="ti-control-pause"></i>
                  </a>
                </li>
                <li>
                  <a
                    class="icon-btn btn-danger button-effect btn-xl is-animating"
                    href="#"
                    data-dismiss="modal"
                    data-tippy-content="Hangup"
                    @click.prevent="endCall"
                  >
                    <phone-icon size="1.5x" class="custom-class"></phone-icon>
                  </a>
                </li>
                <li>
                  <a
                    class="icon-btn btn-light button-effect mic"
                    href="#"
                    data-tippy-content="Mute"
                    @click.prevent="muteMicrophone"
                  >
                    <i class="fa fa-microphone"></i>
                  </a>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import WebRTC from "vue-webrtc";
import {
  MessageSquareIcon,
  VideoIcon,
  SmileIcon,
  SendIcon,
  ChevronLeftIcon,
  SearchIcon,
  PhoneIcon,
} from "vue-feather-icons";

export default {
  name: "message-component",
  props: ["friend"],
  data() {
    return {
      chats: [],
      message: null,
      isTyping: false,
      img: null,
      roomId: "000000000000001",
      chat_src: window.LaravelAsset + "chat/",
      uploads_src: window.LaravelAsset + "uploads/",
      images_src: location.href.includes("message/")
        ? "../calisanim_v1/front/images/"
        : "./calisanim_v1/front/images/",
    };
  },
  computed: {
    session() {
      return this.friend.session;
    },
    can() {
      return this.session.blocked_by == window.Laravel.auth;
    },
  },
  watch: {
    message(value) {
      if (value) {
        Echo.private(`Chat.${this.friend.session.id}`).whisper("typing", {
          name: window.Laravel.auth,
        });
      }
    },
    chats() {
      this.sendLastInfo();
    },
  },
  methods: {
    answer() {
      axios
        .post(window.LaravelUrl + "/response_call", {
          response: "answer",
          session_id: this.session.id,
        })
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      document.getElementsByClassName("icon-btn-call")[0].click();
    },
    decline() {
      axios
        .post(window.LaravelUrl + "/response_call", {
          response: "decline",
          session_id: this.session.id,
        })
        .then(function (response) {
          console.log("message decline");
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    endCall() {
      this.onLeave();

      let container = document.getElementsByClassName(
        "video-chat-container"
      )[0];
      axios
        .post(window.LaravelUrl + "/response_call", {
          response: "endCall",
          session_id: this.session.id,
        })
        .then(function (response) {
          console.log(response.data);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    sendLastInfo() {
      let last_chat = this.chats[this.chats.length - 1];
      if (last_chat) {
        let last_message = {};
        last_message.content = last_chat.message;

        last_chat.send_at = last_chat.send_at.split(":");
        last_chat.send_at[0] =
          last_chat.send_at[0].length == 1
            ? "0" + last_chat.send_at[0]
            : last_chat.send_at[0];
        last_chat.send_at[1] =
          last_chat.send_at[1].length == 1
            ? "0" + last_chat.send_at[1]
            : last_chat.send_at[1];
        last_chat.send_at = last_chat.send_at[0] + ":" + last_chat.send_at[1];

        last_message.send_at = last_chat.send_at;
        last_message.friend_id = this.friend.id;
        this.$emit("sendLastInfo", last_message);
      }
    },
    setVideoSettings() {
      setTimeout(() => {
        let video = this.$refs.webrtc.$refs.videos;
        let video_item = document.getElementsByClassName("video-item");
        video_item[0].style.height = "100%";
        video_item[0].style.width = "100%";
        video_item[0].style.position = "absolute";
        video_item[0].style.zIndex = "2";
        video_item[0].style.backgroundColor = "rgba(0,0,0,0)";
        video_item[0].style.transform = "rotateY(180deg)";
        video[0].controls = false;
        video[0].style.height = "auto";
        video[0].style.width = "25%";
        video[0].style.overflow = "hidden";
        video[0].style.position = "absolute";
        video[0].style.bottom = "0";
        video[0].style.right = "0";
        video[0].style.zIndex = "700";
        video[0].style.objectFit = "cover";
        if (video[1]) {
          video_item[1].style.height = "100%";
          video_item[1].style.width = "100%";
          video_item[1].style.zIndex = "1";
          video_item[1].style.backgroundColor = "#eee";
          video_item[1].style.position = "absolute";
          video_item[1].style.transform = "rotateY(180deg)";
          video[1].controls = false;
          video[1].style.zIndex = "600";
          // document.getElementsByClassName(
          //   "connection-waiting"
          // )[0].style.display = "none";
          video[1].style.height = "100%";
          video[1].style.width = "100%";
          video[1].style.objectFit = "cover";
        }
      }, 2000);
    },
    muteMicrophone() {
      this.$refs.webrtc.enableAudio = !this.$refs.webrtc.enableAudio;
      document
        .getElementsByClassName("mute-btn")[0]
        .classList.toggle("text-danger");
    },
    hideCamera() {
      this.$refs.webrtc.enableVideo = !this.$refs.webrtc.enableVideo;
      document
        .getElementsByClassName("hide-btn")[0]
        .classList.toggle("text-danger");
    },
    openSideChat() {},
    onCapture() {
      this.img = this.$refs.webrtc.capture();
    },
    logEvent(event) {
      console.log("Event : ", event);
    },
    onError(error, stream) {
      console.log("On Error Event", error, stream);
    },
    onJoin() {
      console.log(this.$refs.webrtc.roomId);
      this.$refs.webrtc.join();

      this.sendCallingInfo();
    },
    sendCallingInfo() {
      axios
        .post(window.LaravelUrl + "/send_calling_info", {
          session_id: this.friend.session.id,
          receiver_id: this.friend.id,
          caller_id: window.user_id,
        })
        .then(function (response) {
          console.log("SendCallingInfo() " + response.data);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    onLeave() {
      this.$refs.webrtc.leave();
    },
    onShareScreen() {
      this.img = this.$refs.webrtc.shareScreen();
    },
    onError(error, stream) {
      console.log("On Error Event", error, stream);
    },
    logEvent(event) {
      console.log("Event : ", event);
    },
    startvideochat() {
      //
      // const video = document.querySelector('video');
      // navigator.mediaDevices
      //     .getUserMedia({
      //         video: {
      //             width: 426,
      //             height: 240
      //         }
      //     })
      //     .then((stream) => video.srcObject = stream);
      // Echo.private(`Video.${this.friend.session.id}`).listenForWhisper(
      //     "frame",
      //     e => {
      //         const img = document.querySelector('img');
      //         img.src = e.video
      //         // console.log(e);
      //     }
      // );
      // Echo.join(`Video.${this.friend.session.id}`)
      //     .listen(                        e => {
      //         const img = document.querySelector('img');
      //         img.src = e.video
      //     });
      // setInterval(this.frameLoader, 100 / 30);
    },
    frameLoader() {
      Echo.private(`Video.${this.friend.session.id}`).whisper("frame", {
        video: this.getFrame(),
        session: this.friend.session.id,
      });
    },
    getFrame() {
      const video = document.querySelector("video");
      const canvas = document.createElement("canvas");
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      canvas.getContext("2d").drawImage(video, 0, 0);
      const data = canvas.toDataURL("image/png");
      return data;
    },
    send() {
      if (this.message) {
        console.log("send");
        this.pushToChats(this.message);
        axios
          .post(window.LaravelUrl + `/send/${this.friend.session.id}`, {
            content: this.message,
            to_user: this.friend.id,
          })
          .then((res) => (this.chats[this.chats.length - 1].id = res.data));

        this.message = null;
      }
      $(".message-area").focus();
    },
    goBottom() {
      let chat_area = document.getElementById("chating");
      chat_area.scrollTop = chat_area.scrollHeight;
    },
    pushToChats(message) {
      this.chats.push({
        message: message,
        type: 0,
        read_at: null,
        send_at: new Date().getHours() + ":" + new Date().getMinutes(),
      });
    },
    close() {
      this.$emit("close");
    },
    clear() {
      axios
        .post(window.LaravelUrl + `/session/${this.friend.session.id}/clear`)
        .then((res) => (this.chats = []));
    },
    block() {
      this.session.block = true;
      axios
        .post(window.LaravelUrl + `/session/${this.friend.session.id}/block`)
        .then((res) => (this.session.blocked_by = window.Laravel.auth));
    },
    backToHome(){
      console.log("mobile-sidebar")
      $('.chitchat-container').toggleClass("mobile-menu");
    },
    unblock() {
      this.session.block = false;
      axios
        .post(window.LaravelUrl + `/session/${this.friend.session.id}/unblock`)
        .then((res) => (this.session.blocked_by = null));
    },
    getAllMessages() {
      axios
        .post(window.LaravelUrl + `/session/${this.friend.session.id}/chats`)
        .then((res) => (this.chats = res.data.data));
    },
    read() {
      axios.post(window.LaravelUrl + `/session/${this.friend.session.id}/read`);
    },
  },

  created() {
    this.read();
    this.getAllMessages();
    console.log(this.$refs);
    Echo.private(`Chat.${this.friend.session.id}`).listen(
      "PrivateChatEvent",
      (e) => {
        this.friend.session.open ? this.read() : "";
        this.chats.push({
          message: e.content,
          type: 1,
          send_at: new Date().getHours() + ":" + new Date().getMinutes(),
        });
      }
    );

    Echo.private(`Chat.${this.friend.session.id}`).listen("MsgReadEvent", (e) =>
      this.chats.forEach((chat) =>
        chat.id == e.chat.id ? (chat.read_at = e.chat.read_at) : ""
      )
    );

    Echo.private(`Chat.${this.friend.session.id}`).listen(
      "BlockEvent",
      (e) => (this.session.block = e.blocked)
    );

    Echo.private(`Chat.${this.friend.session.id}`).listenForWhisper(
      "typing",
      (e) => {
        this.isTyping = true;
        setTimeout(() => {
          this.isTyping = false;
        }, 2000);
      }
    );
  },
  updated() {
    let chat_area = document.getElementById("chating");
    if (
      chat_area.scrollTop + chat_area.clientHeight >=
        chat_area.scrollHeight - 400 &&
      this.message == null &&
      chat_area.clientHeight !== chat_area.scrollHeight
    ) {
      this.goBottom();
    }
  },
  mounted() {
    this.$nextTick(function () {
      setTimeout(() => this.goBottom(), 600);
    });
  },
  components: {
    MessageSquareIcon,
    ChevronLeftIcon,
    SearchIcon,
    PhoneIcon,
    SendIcon,
    SmileIcon,
    VideoIcon,
  },
};
</script>
<style scoped>
.bg-img {
  object-fit: cover;
  display: block;
  height: 100%;
  border-radius: 20px;
}
.videocall .bg-img {
  border-radius: 0px;
  width: 100%;
}
.media {
  margin-top: 0px;
}
.message-input {
  margin: 0;
}
.chat-content {
  z-index: unset !important;
}
.modal-dialog-centered {
  pointer-events: unset;
}
.videocall {
  background-color: #ddd;
}
.videocall-details {
  z-index: 20;
  background-color: rgba(255,255,255,0.2);
}
.center-con {
  z-index: 20;
  padding: 15px 0;
  width: 240px !important;
  margin-left: calc(50% - 120px);
  border-radius: 15px;
  background-color: rgba(255,255,255,0.2);
}
</style>