<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import { io, Socket } from "socket.io-client";
import SwiperClass from "swiper";
import { Swiper, SwiperSlide } from "swiper/vue";
import SwiperCore, { EffectCoverflow,Pagination } from 'swiper';
import 'swiper/css';
import "swiper/css/effect-coverflow"
import "swiper/css/pagination"
import '../style.css';

SwiperCore.use([EffectCoverflow,Pagination]);

const swiperRef = ref();

const onSwiper = (swiper: SwiperClass) => {
  console.log(swiper);
  swiperRef.value = swiper;
};
const onSlideChange = (swiper: SwiperClass) => {
  console.log("onSlideChange", swiperRef.value.activeIndex);
  selectedTheMovie(store.files[swiperRef.value.activeIndex]);
};

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
  <swiper
    :effect="'coverflow'"
    :grabCursor="true"
    :centeredSlides="true"
    :slidesPerView="'auto'"
    :coverflowEffect='{
      "rotate": 30,
      "stretch": 0,
      "depth": 100,
      "modifier": 1,
      "slideShadows": true
    }'
    :pagination="true"
    @swiper="onSwiper"
    @slide-change="onSlideChange"
  >
    <swiper-slide v-for="(file, index) in store.files" :key="index">
      <div>{{ file }}</div>
    </swiper-slide>
  </swiper>
</template>
