<template>
  <div class="w-full h-full">
    <div v-if="loading"
      class="bg-[url('assets/images/bg.png')] bg-cover bg-center loading-container h-screen w-full flex justify-center items-center">
      <div class="loading"></div>
    </div>
    <div class="bg-[url('assets/images/bg.png')] bg-cover bg-center h-screen text-white p-3 flex overflow-hidden"
      v-else>
      <!-- left -->
      <div class="flex-1 mr-3 bg-opacity-50 bg-slate-800 p-3">
        <!-- 横向柱状图 -->
        <HorizontalBar class="h-1/3 box-border pb-2" :data="data.regionData" />
        <!-- 雷达图 -->
        <RadarBar class="h-1/3 box-border pb-2" :data="data.riskData" />
        <!-- 关系图 -->
        <Relation class="h-1/3" :data="data.relationData" />
      </div>
      <!-- center -->
      <div class="w-1/2 mr-3 flex flex-col">
        <!-- 数据总览图 -->
        <TotalData class="bg-opacity-50 bg-slate-800 p-3" :data="data.totalData" />
        <!-- 地图可视化 -->
        <MapChart class="bg-opacity-50 bg-slate-800 p-3 mt-3 flex-1" :data="data.mapData" />
      </div>
      <!-- right -->
      <div class="flex-1 bg-opacity-50 bg-slate-800 p-3 flex flex-col">
        <!-- 竖向柱状图 -->
        <VerticalBar class="h-1/3 box-border pb-2" :data="data.verData" />
        <!-- 环形图 -->
        <RingBar class="h-1/3 box-border pb-2" :data="data.abnormalData" />
        <!-- 文档云图 -->
        <WordCloud class="h-1/3" :data="data.wordCloudData" />
      </div>
    </div>
  </div>
</template>

<script setup>
import HorizontalBar from "@/components/HorizontalBar.vue";
import RadarBar from "@/components/RadarBar.vue";
import Relation from "@/components/Relation.vue";
import TotalData from "@/components/TotalData.vue";
import MapChart from "@/components/MapChart.vue";
import VerticalBar from "@/components/VerticalBar.vue";
import RingBar from "@/components/RingBar.vue";
import WordCloud from "@/components/WordCloud.vue";

import { reactive, ref } from "vue";
import { getVisualization } from "../../api/visualization";
import { useRouter } from "vue-router";

import { throttleTimeAndTimer } from "../../utils/utils";

// 本地mock数据
// import mockData from "../../utils/mockData.js";
// let _mockData = reactive(mockData);

// 加载状态
const loading = ref(true);

const data = ref(null);
const loadData = async () => {
  const _mockData = await import("../../utils/mockData.js");
  // data.value = await getVisualization();
  data.value = _mockData.default;
  console.log(data.value)
  loading.value = false;
};
loadData();

const $router = useRouter();
// 获取宽度
const windowSize = () => {
  let width = document.documentElement.clientWidth;
  width <= 768 ? $router.push({ path: "/m" }) : "";
  loading.value = true
};
window.addEventListener("resize", windowSize);
window.onresize = () => {
  // 页面大小变化时，刷新页面
  windowSize();
  throttleTimeAndTimer(window.history.go(0), 500);
};
windowSize();

setInterval(() => {
  loadData();
}, 3000);
</script>

<style scoped lang="scss">
.loading-container {
  position: fixed;
  top: 0;
  left: 0;
}

@import '@/assets/style/loading.scss';
</style>
