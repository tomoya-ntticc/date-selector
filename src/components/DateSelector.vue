<script setup lang="ts">
import { reactive, onMounted } from 'vue'
import { io, Socket } from "socket.io-client";

const store = reactive({
  files: [""]
})

const socket: Socket = io('0.0.0.0:3000');
const requestTheMovieList = () => {
  console.log("requestTheMovieList");
  socket.emit("requestTheMovieList");
}
const selectedTheMovie = (fileName: String) => {
  console.log("selectedTheMovie");
  socket.emit("selectedTheMovie", fileName);
}
const responseTheMovieList = (files: Array<string>) => {
  console.log("responseTheMovieList:", files);
  store.files = files;
}
const finishedTheMovie = () => {
  console.log("finishedTheMovie");
}

onMounted(() => {
  requestTheMovieList();
  socket.on("responseTheMovieList", (files) => {
    responseTheMovieList(files);
  });
  socket.on("finishedTheMovie", () => {
    finishedTheMovie();
  });
})
</script>

<template>
  <button v-on:click="requestTheMovieList">requestTheMovieList</button>
  <ul>
    <li v-for="(file, index) in store.files" :key="index">{{ file }}</li>
  </ul>
  <button v-on:click="selectedTheMovie('2021_08_27')">selectedTheMovie</button>
</template>
