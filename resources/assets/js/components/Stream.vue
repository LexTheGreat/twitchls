<template>
  <div class="stream">
    <div class="btn-group stream__buttons stream__buttons--hidden" role="group" id="buttonsMobile">
      <button
        class="btn btn-default stream__button stream__button--overlayed icon__left"
        type="button"
        @click="toggleChatMobile"
        title="Show/Hide Twitch chat"
      ></button>

      <button
        class="btn btn-default stream__button stream__button--overlayed icon__close"
        type="button"
        @click="exitStreamMobile"
        title="Close stream"
      ></button>
    </div>

    <div class="btn-group stream__buttons stream__buttons--hidden-sm" role="group" id="buttons">
      <button
        class="btn btn-default stream__button icon__right"
        type="button"
        @click="toggleChat"
        title="Show/Hide Twitch chat"
      ></button>

      <button
        class="btn btn-default stream__button icon__fullscreen"
        type="button"
        id="fullscreen"
        @click="toggleFullscreen"
        title="Toggle FullScreen"
      ></button>
    </div>

    <div class="stream__chat stream__chat--hidden-sm" id="chat">
      <iframe
        frameborder="0"
        scrolling="no"
        id="twitch_embed_chat"
        src="https://www.twitch.tv/embed/{{ $route.params.channel }}/chat?&parent=twitchls.com&parent=www.twitchls.com&parent=test.twitchls.com"
      ></iframe>
    </div>
    <div class="stream__player" id="player">
      <iframe
        src="https://player.twitch.tv/?channel={{ $route.params.channel }}&html5={{ html5playback }}&parent=twitchls.com&parent=www.twitchls.com&parent=test.twitchls.com"
        frameborder="0"
        scrolling="no"
        allowfullscreen="no"
      ></iframe>
      <div id="dragbar" class="stream__dragbar stream__dragbar--hidden-sm"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Stream",

  data() {
    return {
      html5playback: true
    };
  },

  attached() {
    this.html5playback = JSON.parse(localStorage.getItem("html5playback"));

    if (this.html5playback === null) {
      this.html5playback = true;
    }

    const dragbar = document.querySelector("#dragbar");
    const player = document.querySelector("#player");
    const chat = document.querySelector("#chat");
    let mouseIsDown = false;

    if (localStorage.getItem("player_width") !== null) {
      player.style.width = localStorage.getItem("player_width");
      chat.style.left = localStorage.getItem("player_width");
    }

    dragbar.addEventListener("mousedown", event => {
      mouseIsDown = true;
    });

    document.addEventListener("mouseup", event => {
      mouseIsDown = false;
    });

    document.addEventListener("mousemove", event => {
      if (mouseIsDown) {
        player.style.width = event.pageX + "px";
        chat.style.left = event.pageX + "px";

        localStorage.setItem("player_width", player.style.width);
      }
    });
  },

  methods: {
    toggleChatMobile(event) {
      event.target.classList.toggle("icon__right");
      event.target.classList.toggle("icon__left");

      const buttons = document.getElementById("buttonsMobile");

      Array.prototype.forEach.call(buttons.children, el => {
        el.classList.toggle("stream__button--overlayed");
      });

      chat.classList.toggle("stream__chat--hidden-sm");

      localStorage.removeItem("player_width");

      player.style.width = "inherit";
      chat.style.left = "inherit";
    },

    exitStreamMobile(event) {
      if (window.history.length === 0) {
        this.$route.router.go("/");
        return;
      }

      this.$route.router.go(window.history.back());
    },

    toggleChat(event) {
      event.target.classList.toggle("icon__right");
      event.target.classList.toggle("icon__left");

      const buttons = document.getElementById("buttons");

      Array.prototype.forEach.call(buttons.children, el => {
        el.classList.toggle("stream__button--overlayed");
      });

      chat.classList.toggle("stream__chat--hidden");
      dragbar.classList.toggle("stream__dragbar--hidden");
      player.classList.toggle("stream__player--full");
    },

    toggleFullscreen(event) {
      event.target.classList.toggle("icon__fullscreen");
      event.target.classList.toggle("icon__fullscreen-exit");

      if (
        !document.fullscreenElement &&
        !document.mozFullScreenElement &&
        !document.webkitFullscreenElement &&
        !document.msFullscreenElement
      ) {
        if (document.documentElement.requestFullscreen) {
          document.documentElement.requestFullscreen();
        } else if (document.documentElement.msRequestFullscreen) {
          document.documentElement.msRequestFullscreen();
        } else if (document.documentElement.mozRequestFullScreen) {
          document.documentElement.mozRequestFullScreen();
        } else if (document.documentElement.webkitRequestFullscreen) {
          document.documentElement.webkitRequestFullscreen(
            document.ALLOW_KEYBOARD_INPUT
          );
        }
      } else {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.webkitExitFullscreen) {
          document.webkitExitFullscreen();
        }
      }
    }
  }
};
</script>
