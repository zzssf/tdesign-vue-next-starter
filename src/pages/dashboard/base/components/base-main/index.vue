<template>
  <div class="base-main-wrapper">
    <home-swiper />
  </div>
</template>

<script lang="tsx">
export default {
  name: 'BaseMain',
};
</script>

<script setup lang="tsx">
import HomeSwiper from './components/home-swiper/index.vue';
</script>

<style lang="less" scoped>
@import './index.less';
</style>
