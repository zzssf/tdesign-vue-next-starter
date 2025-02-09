<template>
  <div class="dashboard-base-wrapper">
    <base-main />
    <base-right />
    <!-- 顶部 card  -->
    <!-- <top-panel class="row-container" /> -->
    <!-- 中部图表  -->
    <!-- <middle-chart class="row-container" /> -->
    <!-- 列表排名 -->
    <!-- <rank-list class="row-container" /> -->
    <!-- 出入库概览 -->
    <!-- <output-overview class="row-container" /> -->
  </div>
</template>

<script lang="ts">
export default {
  name: 'DashboardBase',
};
</script>

<script setup lang="ts">
import BaseMain from './components/base-main/index.vue';
import BaseRight from './components/base-right/index.vue';
// import MiddleChart from './components/MiddleChart.vue';
// import OutputOverview from './components/OutputOverview.vue';
// import RankList from './components/RankList.vue';
// import TopPanel from './components/TopPanel.vue';
</script>

<style scoped>
@import './index.less';
</style>
