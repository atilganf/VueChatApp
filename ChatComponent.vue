<template>
  <div class="cont">
    <!-- Chat Component -->
    <!-- Sayfa yüklenirken gösterilen kısım -->
    <!-- <div class="chitchat-loader">
      <div>
        <img :src="chat_src + 'images/logo/logo.png'" alt />
        <h3>Simple, secure messaging for fast connect to world..!</h3>
      </div>
    </div>-->

    <div class="call-answer-cont">
      <!-- <audio id="ringtone" loop preload="auto">
        <source :src="audio_src + 'ringtone.mp3'" type="audio/mpeg" />
        <source :src="audio_src + 'ringtone.ogg'" type="audio/ogg" />
      </audio> -->
      <div class="caller-cont">
        <img style="object-fit: contain;" id="caller-img" />
        <h4 id="caller-name"></h4>
      </div>
      <div class="answer-btn-cont">
        <button class="answer-btn" id="answ-btn-camera" @click.prevent="answer">
          <i class="fa fa-video-camera" aria-hidden="true"></i>
        </button>
        <button class="answer-btn" id="answ-btn-phone" @click.prevent="decline">
          <i class="fa fa-phone" aria-hidden="true"></i>
        </button>
      </div>
    </div>

    <div class="chitchat-container sidebar-toggle">
      <!-- Sol kısım -->
      <aside class="chitchat-left-sidebar left-disp">
        <div class="recent-default dynemic-sidebar active">
          <!-- Sol üst logo -->
          <div class="recent">
            <div class="theme-title text-center">
              <a :href="laravel_url">
                <img class="w-50 mx-auto" :src="chat_src + 'images/logo/logo.png'" alt="logo" />
              </a>
            </div>
          </div>
          <div class="chat custom-scroll">
            <!-- Sol üst Başlık/Arama -->
            <div class="theme-title">
              <div class="media">
                <div>
                  <h2>Chat</h2>
                  <h4>Yeni Görüşme Başlat</h4>
                </div>
                <div class="media-body text-right">
                  <a class="icon-btn btn-outline-light btn-sm search contact-search">
                    <search-icon size="1.5x" class="custom-class"></search-icon>
                  </a>
                  <form class="form-inline search-form">
                    <div class="form-group">
                      <input
                        placeholder="Ara !"
                        name="search"
                        id="search"
                        @keyup="search"
                        @change.prevent
                        class="form-control-plaintext"
                        type="search"
                      />
                      <div class="icon-close close-search"></div>
                    </div>
                  </form>
                </div>
              </div>
            </div>
            <div class="theme-tab tab-sm chat-tabs">
              <!-- Kişiler Tab ı -->
              <ul class="nav nav-tabs nav-chat-tabs" id="myTab" role="tablist">
                <li class="nav-item w-100 mx-2" data-to="chat-content">
                  <a
                    class="nav-link button-effect active"
                    id="chat-tab"
                    data-toggle="tab"
                    href="#chat"
                    role="tab"
                    aria-controls="chat"
                    aria-selected="true"
                    data-intro="Start chat"
                  >
                    <message-square-icon size="1.5x" class="custom-class"></message-square-icon>Kişiler
                  </a>
                </li>
              </ul>
              <div class="tab-content" id="myTabContent">
                <div
                  class="tab-pane fade show active"
                  id="chat"
                  role="tabpanel"
                  aria-labelledby="chat-tab"
                >
                  <div class="theme-tab">
                    <div class="tab-content" id="myTabContent1">
                      <!-- Soldaki kişiler kısmı -->
                      <div
                        class="tab-pane fade show active"
                        id="direct"
                        role="tabpanel"
                        aria-labelledby="direct-tab"
                      >
                        <ul class="chat-main">
                          <li
                            :style="{display: friend.display ? 'block': 'none'}"
                            @click="openChat(friend)"
                            :key="friend.id"
                            :class="friend.session.open ? 'active' : ''"
                            v-for="friend in friends"
                            data-to="blank"
                          >
                            <div class="chat-box">
                              <div
                                :class="friend.online ? 'online': 'offline'"
                                class="profile"
                              >
                                <img class="bg-img" :src="uploads_src + friend.image" alt="Avatar" />
                              </div>
                              <div class="details">
                                <h5>{{friend.name}}</h5>
                                <h6>{{friend.last_message.content}}</h6>
                              </div>
                              <div class="date-status">
                                <i class="ti-pin2"></i>
                                <h6>{{friend.last_message.send_at}}</h6>
                                <div
                                  v-if="friend.session && (friend.session.unreadCount > 0)"
                                  class="badge badge-primary sm"
                                >{{friend.session.unreadCount}}</div>
                              </div>
                            </div>
                          </li>
                        </ul>
                      </div>
                      <!-- Soldaki kişiler kısmı END-->
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </aside>
      <!-- Message component -->
      <div
        v-for="friend in friends"
        :key="friend.id"
        v-if="friend.session.open"
        class="chitchat-main w-100"
        id="content"
      >
        <message-component
          v-on:sendLastInfo="getLastInfo"
          v-if="friend.session.open"
          @close="close(friend)"
          :friend="friend"
          ref="message"
        ></message-component>
      </div>
    </div>
  </div>
</template>

<script>
import MessageComponent from "./MessageComponent";
import {
  MessageSquareIcon,
  VideoIcon,
  SmileIcon,
  SendIcon,
  ChevronLeftIcon,
  SearchIcon,
  PhoneIcon,
} from "vue-feather-icons";
require("howler");

export default {
  props: ["user"],

  data() {
    return {
      friends: [],
      uploads_src: this.user ? "../uploads/" : "./uploads/",
      public_src: window.LaravelAsset,
      chat_src: window.LaravelAsset + "chat/",
      audio_src: this.user
        ? "../calisanim_v1/front/audio/"
        : "./calisanim_v1/front/audio/",
      call_friend_id: 0,
      laravel_url : window.LaravelUrl + '/',
      user_id: window.user_id,
      isAnswered: false,
      soundIsplaying: false,
      sound: null,
    };
  },
  mounted() {
    this.sound = new Howl({
      src: window.LaravelAsset + "/calisanim_v1/front/audio/ringtone.mp3",
      volume: 0.5,
      loop: true,
      preload: true,
      html5: true,
      format: ["mp3", "aac"],
      onplayerror : function(error){
        console.log(error);
      }
    });
    this.getFriends();
    setTimeout(() => {
      for (let i = 0; i < this.friends.length; i++) {
        this.openChat(this.friends[i]);
      }
      if (this.user) {
        let user_id = JSON.parse(this.user).id;
        this.openChat(this.friends.find((f) => f.id == user_id));
      } else {
        this.openChat(this.friends[0]);
      }
    }, 2000);

    window.addEventListener(
      "beforeunload",
      function (e) {
        axios
          .post(window.LaravelUrl + "/chat_close_action")
          .then(function (response) {
            console.log(response);
          })
          .catch(function (error) {
            console.log(error);
          });
      },
      false
    );

    this.checkCalls();
  },
  methods: {
    answer() {
      this.sound.stop();
      this.soundIsplaying = false;
      this.stopCall();
      this.openChat(this.friends.find((f) => f.id == this.call_friend_id));
      this.isAnswered = true;
      let that = this;
      setTimeout(function () {
        that.$children[0].answer();
      }, 500);
      this.checkCalls();
    },
    decline() {
      this.sound.stop();
      this.soundIsplaying = false;
      this.stopCall();
      this.openChat(this.friends.find((f) => f.id == this.call_friend_id));
      this.isAnswered = true;
      let that = this;
      setTimeout(function () {
        that.$children[0].decline();
      }, 500);
      this.checkCalls();
    },
    checkCalls() {
      let that = this;
      let interval = setInterval(() => {
        axios
          .post(window.LaravelUrl + "/check_calls", {
            user_id: window.user_id,
          })
          .then((response) => {
            let data = response.data;

            if (data["message"] == "receiver: arama yapılıyor") {
              if (that.isAnswered) {
                that.isAnswered = false;
              } else {
                // $(".connection-h1").html("Bağlantı bekleniyor...");
                // $(".connection-h1").css("color", "#fff");
                // $(".connection-h1").css(
                //   "text-shadow",
                //   "0 0 5px #fff, 0 0 10px #fff"
                // );
                console.log("receiver: arama yapılıyor");
                if (data["friend"] != that.user_id) {
                  $(".call-answer-cont").css("display", "grid");
                  if (!that.soundIsplaying) {
                    that.sound.play();
                    that.soundIsplaying = true;
                  }
                  console.log(data);
                  $("#caller-img").attr(
                    "src",
                    that.uploads_src + data["caller_img"]
                  );
                  $("#caller-name").html(data["caller_name"]);

                  that.session_id = data["session_id"];
                  that.call_friend_id = response.data["friend"];
                }
              }
              console.log(data["message"]);
            } else if (data == "receiver: arama reddedildi") {
              //
              that.stopCall();
              console.log(data);
              that.sound.stop();
              that.sound.unload();
              that.soundIsplaying = false;
            } else if (data == "receiver: gorusme yapılıyor") {
              //
              that.stopCall();
              that.soundIsplaying = false;
              that.sound.stop();
              that.sound.unload();
              console.log(data);
            } else if (data == "caller: arama yapılıyor") {
              // $(".connection-h1").html("Bağlantı bekleniyor...");
              // $(".connection-h1").css("color", "#fff");
              // $(".connection-h1").css(
              //   "text-shadow",
              //   "0 0 5px #fff, 0 0 10px #fff"
              // );
              console.log(data);
            } else if (data == "caller: arama reddedildi") {
              //
              that.stopCall();
              that.soundIsplaying = false;

              that.sound.stop();
              that.sound.unload();
              console.log(data);
            } else if (data == "caller: gorusme yapılıyor") {
              //
              that.stopCall();
              that.sound.stop();
              that.soundIsplaying = false;
              that.sound.unload();
              console.log(data);
            } else if (data == "arama yok") {
              that.stopCall();
              // $(".connection-h1").html("Arama sonlandırıldı.");
              // $(".connection-h1").css("color", "#dc3545");
              // $(".connection-h1").css(
              //   "text-shadow",
              //   "0 0 5px #dc3545, 0 0 10px #dc3545"
              // );
              console.log(data);
            }
          })
          .catch(function (error) {
            console.log(error);
          });
      }, 2000);
    },
    stopCall() {
      // $("audio").each(function () {
      //   this.pause(); // Stop playing
      //   this.currentTime = 0; // Reset time
      // });
      $(".call-answer-cont").css("display", "none");
    },
    getLastInfo(value) {
      let newValue = this.friends.find((f) => f.id == value.friend_id);
      let index = this.friends.findIndex((f) => f.id == value.friend_id);
      newValue.last_message = value;
      Vue.set(this.friends, index, newValue);
    },
    close(friend) {
      friend.session.open = false;
    },
    getFriends() {
      axios.post(window.LaravelUrl + "/getFriends").then((res) => {
        this.friends = res.data.data;
        this.friends.forEach((friend) => {
          friend.session ? this.listenForEverySession(friend) : "";
          friend["display"] = true;
        });
        if (this.user) {
          var user;
          user = JSON.parse(this.user);
          let friend = this.friends.find((friend) => friend.id == user.id);
          friend.session.open = true;
          friend.session.unreadCount = 0;
        }
      });
    },
    openChat(friend) {
      if (friend.session) {
        this.friends.forEach((friend) =>
          friend.session ? (friend.session.open = false) : ""
        );
        friend.session.open = true;
        friend.session.unreadCount = 0;
      } else {
        this.createSession(friend);
      }
      $('.chitchat-container').toggleClass("mobile-menu");
      // let rightDiv = document.getElementsByClassName("right")[0];
      // rightDiv.classList.add("right-active");
    },
    closeChat(friend) {
      friend.session.open = false;
      // let rightDiv = document.getElementsByClassName("right")[0];
      // rightDiv.classList.remove("right-active");
    },
    createSession(friend) {
      axios
        .post(window.LaravelUrl + "/session/create", {
          friend_id: friend.id,
        })
        .then((res) => {
          (friend.session = res.data.data), (friend.session.open = true);
        });
    },
    listenForEverySession(friend) {
      Echo.private(`Chat.${friend.session.id}`).listen(
        "PrivateChatEvent",
        (e) => (friend.session.open ? "" : friend.session.unreadCount++)
      );
    },
    search() {
      let val = document.getElementById("search").value;
      for (let i = 0; i < this.friends.length; i++) {
        let newFriend = this.friends[i];
        if (this.friends[i].name.toLowerCase().includes(val.toLowerCase())) {
          newFriend["display"] = true;
        } else {
          newFriend["display"] = false;
        }

        Vue.set(this.friends, i, newFriend);
      }
    },
  },
  created() {
    Echo.channel("Chat").listen("SessionEvent", (e) => {
      let friend = this.friends.find((friend) => friend.id == e.session_by);
      friend.session = e.session;
      this.listenForEverySession(friend);
    });

    Echo.join(`Chat`)
      .here((users) => {
        this.friends.forEach((friend) => {
          users.forEach((user) => {
            if (user.id == friend.id) {
              friend.online = true;
            }
          });
        });
      })
      .joining((user) => {
        this.friends.forEach((friend) =>
          user.id == friend.id ? (friend.online = true) : ""
        );
      })
      .leaving((user) => {
        this.friends.forEach((friend) =>
          user.id == friend.id ? (friend.online = false) : ""
        );
      });
    // window.onresize = function() {
    //   document.body.height = window.innerHeight;
    // };
    // window.onresize();
  },
  components: {
    MessageComponent,
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
/* call container */
.call-answer-cont {
  height: 75px;
  position: absolute;
  top: 0;
  width: 100%;
  display: none;
  grid-template-columns: auto 150px;
  z-index: 9999;
  background-color: rgb(43, 44, 51, 0.7);
}
.caller-cont {
  height: 73px;
  position: relative;
  display: flex;
  padding-top: 15px;
  padding-bottom: 15px;
  padding-left: 28px;
}
#caller-img {
  height: 45px;
  width: 45px;
  object-fit: contain;
  margin-bottom: 11px;
  display: inline-block;
  background-color: #fff;
  border-radius: 50%;
}
#caller-name {
  line-height: 45px;
  display: inline-block;
  color: white;
  padding-left: 10px;
  margin-bottom: 11px;
}
.answer-btn-cont {
  padding-top: 15px;
  display: inline-block;
  text-align: right;
  padding-right: 28px;
}
.answer-btn {
  background-color: #4caf50;
  border-radius: 50%;
  height: 45px;
  margin: 0 7px;
  width: 45px;
  color: white;
  font-size: 1.5em;
  line-height: 45px;
  text-align: center;
  border: none;
}
#answ-btn-phone {
  background: #f44336;
  color: white;
}
/* caller cont end */
.ti-pin2 {
  opacity: 0 !important;
}
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
#content{
  z-index: unset !important;
}
</style>