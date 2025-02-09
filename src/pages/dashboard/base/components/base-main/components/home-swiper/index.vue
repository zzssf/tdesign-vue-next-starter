<template>
  <t-swiper class="home-swiper-wrapper" :interval="2000" :duration="300" :height="280">
    <t-swiper-item v-for="(imgUrl, index) in items" :key="index" class="home-swiper-item">
      <t-image :src="imgUrl" fit="cover" class="home-image" />
    </t-swiper-item>
  </t-swiper>
</template>

<script lang="tsx">
export default {
  name: 'HomeSwiper',
};
</script>

<script setup lang="tsx">
import { reactive } from 'vue';

const items = reactive([
  'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
  'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
  'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
]);
</script>

<style lang="less" scoped>
@import './index.less';
</style>
