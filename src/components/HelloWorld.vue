<script setup>
import ZoomVideo from "@zoom/videosdk";
import uitoolkit from "@zoom/videosdk-ui-toolkit";
import "@zoom/videosdk-ui-toolkit/dist/videosdk-ui-toolkit.css";
import { ref } from "vue";

var sessionContainer;
var authEndpoint = "https://mafia-test.caspadm.com/api/v1/game/generateToken";
var config = ref({
  videoSDKJWT: "",
  sessionName: "test game",
  sessionKey: "test game",
  userName: "Vue.js",
  userIdentity: "123",
  features: ["video", "audio", "settings", "users", "chat", "share"],
});
var role = ref(0);

function getVideoSDKJWT() {
  sessionContainer = document.getElementById("sessionContainer");

  document.getElementById("join-flow").style.display = "none";

  fetch(authEndpoint, {
    method: "POST",
    body: JSON.stringify({
      sessionName: config.value.sessionName,
      role: role.value,
      sessionKey: config.value.sessionName,
      userIdentity: config.value.userIdentity,
      username: config.value.userName,
    }),
    headers: {
      Accept: "application/json",
      "Content-Type": "application/json",
    },
  })
    .then((response) => {
      return response.json();
    })
    .then((data) => {
      config.value.videoSDKJWT = data.token;
      joinSession();
    })
    .catch((error) => {
      console.log(error);
    });
}

function joinSession() {
  uitoolkit.joinSession(sessionContainer, config.value);

  uitoolkit.onSessionClosed(sessionClosed);
}

var sessionClosed = () => {
  console.log("session closed");
  uitoolkit.closeSession(sessionContainer);

  document.getElementById("join-flow").style.display = "block";
};

// const client = ZoomVideo.createClient();
// const stream = ref();
// function joinSession() {
//   client.init("en-US", "Global", { patchJsMedia: true }).then(() => {
//     client
//       .join(
//         config.value.sessionName,
//         config.value.videoSDKJWT,
//         config.value.userName
//       )
//       .then(() => {
//         stream.value = client.getMediaStream();
//         renderVideo(stream.value);
//       });
//   });
// }
// const nodes = ref();
// async function renderVideo(stream) {
//   stream.startVideo().then(() => {
//     stream
//       .attachVideo(client.getCurrentUserInfo().userId, 300)
//       .then((userVideo) => {
//         nodes.value = userVideo;
//         // document.querySelector("video-player-container").appendChild(userVideo);
//       });
//   });
// }
</script>

<template>
  <main>
    <div class="" style="display: flex">
      <div class="">
        <label>Role</label>
        <input v-model="role" />
      </div>
      <div class="">
        <label>Username</label>
        <input v-model="config.userName" />
      </div>
      <div class="">
        <label>User Identify</label>
        <input v-model="config.userIdentity" />
      </div>
    </div>
    <div id="join-flow">
      <h1>Zoom Video SDK Sample Vue.js</h1>
      <p>User interface offered by the Video SDK UI Toolkit</p>

      <button @click="getVideoSDKJWT">Join Session</button>
    </div>

    <video-player-container v-html="nodes"></video-player-container>

    <div id="sessionContainer"></div>
  </main>
</template>

<style scoped>
video-player-container {
  width: 100%;
  height: 1000px;
}

video-player {
  width: 100%;
  height: auto;
  aspect-ratio: 16/9;
}
</style>
