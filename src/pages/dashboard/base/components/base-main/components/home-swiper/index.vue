<!-- <template>
  <t-swiper class="home-swiper-wrapper" :interval="2000" :duration="300">
    <t-swiper-item v-for="(imgUrl, index) in items" :key="index">
      <t-image :src="imgUrl" class="swiper-image" />
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
</style> -->

<template>
  <t-swiper
    v-model:current="currentIndex"
    :autoplay="false"
    class="home-swiper-wrapper"
    :interval="2000"
    :duration="300"
    :navigation="{ showSlideBtn: 'never' }"
  >
    <t-swiper-item v-for="(imgUrl, index) in items" :key="index">
      <t-image :src="imgUrl" class="swiper-image" />
    </t-swiper-item>

    <!-- 缩略图及左右箭头 -->
    <template #navigation>
      <div class="thumbnail-container">
        <!-- 左箭头 -->
        <div class="arrow left-arrow" @click="handlePrev"><t-icon name="chevron-left" /></div>

        <!-- 当前缩略图 -->
        <div class="thumbnail-item"><t-image :src="items[currentIndex]" class="thumbnail-image" /></div>

        <!-- 右箭头 -->
        <div class="arrow right-arrow" @click="handleNext"><t-icon name="chevron-right" /></div>
      </div>
    </template>
  </t-swiper>
</template>

<script setup lang="tsx">
import { reactive, ref } from 'vue';

const currentIndex = ref(0); // 当前展示的图片索引
const items = reactive([
  'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
  'https://fuss10.elemecdn.com/8/27/f01c15bb73e1ef3793e64e6b7bbccjpeg.jpeg',
  'https://fuss10.elemecdn.com/1/8e/aeffeb4de74e2fde4bd74fc7b4486jpeg.jpeg',
]);

// 切换到上一张
const handlePrev = () => {
  currentIndex.value = (currentIndex.value - 1 + items.length) % items.length;
};

// 切换到下一张
const handleNext = () => {
  currentIndex.value = (currentIndex.value + 1) % items.length;
};
</script>

<style lang="less" scoped>
.home-swiper-wrapper {
  position: relative;

  // 主图样式
  :deep(.t-image__wrapper) {
    width: 100%;
    height: 280px;

    img {
      object-fit: cover;
    }
  }

  // 缩略图容器
  .thumbnail-container {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 16px; // 缩略图与箭头的间距
    padding: 8px 0;
    background: rgba(0, 0, 0, 0.3);
    position: absolute;
    bottom: 0;
    width: 100%;
  }

  // 单个缩略图
  .thumbnail-item {
    width: 60px;
    height: 40px;
    border: 2px solid #fff;
    border-radius: 4px;
    overflow: hidden;
  }

  // 缩略图图片
  .thumbnail-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  // 箭头样式
  .arrow {
    z-index: 9999;
    cursor: pointer;
    color: #fff;
    font-size: 24px;
    transition: opacity 0.3s;

    &:hover {
      opacity: 0.8;
    }
  }

  // 左箭头
  .left-arrow {
    margin-right: 8px; // 调整左箭头与缩略图的间距
  }

  // 右箭头
  .right-arrow {
    margin-left: 8px; // 调整右箭头与缩略图的间距
  }
}
</style>
