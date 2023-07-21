<template>
  <main style="max-width: 80%; margin: 0 auto 100px">
    <!-- 提示 -->
    <a-alert v-if="!isCnChar" type="error">提示：请输入汉字</a-alert>
    <!-- 输入 -->
    <a-card>
      <a-input
        size="large"
        v-model="input"
        placeholder="请输入汉字"
        allow-clear
      >
      </a-input>
    </a-card>

    <!-- 展示 -->
    <a-card title="文字描写" hoverable>
      <div id="drawAnimation"></div>
    </a-card>
    <!-- 笔顺通读 -->
    <a-card title="笔顺通读" hoverable>
      <div style="padding: 4px 0" v-for="item in strokaist" :key="item.id">
        {{ item.join("、") }}
      </div>
    </a-card>
    <!-- 笔顺描绘 -->
    <a-card title="笔顺描绘" hoverable>
      <div id="drawStroke"></div>
    </a-card>
  </main>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import cnchar from "cnchar";
import draw from "cnchar-draw";
import order from "cnchar-order";
cnchar.use(order); // use 在浏览器环境中非必须
cnchar.use(draw); // use 在浏览器环境中非必须

const input = ref("学");
const isCnChar = ref(false);
const strokaist = ref([]);

// 设置
const setChar = (str) => {
  if (cnchar.isCnChar(str)) {
    isCnChar.value = true;
    cnchar.draw(str, {
      el: "#drawStroke",
      type: cnchar.draw.TYPE.STROKE,
    });
    cnchar.draw(str, {
      el: "#drawAnimation",
      type: cnchar.draw.TYPE.ANIMATION,
      animation: {
        strokeAnimationSpeed: 1, // : 1, // 数值, 默认 1。 绘制每个笔划的速度必须大于0。增加此数字可以更快地绘制笔划，减少绘制笔划的速度更慢。
        daayBetweenStrokes: 500, // : 1000, // 数值, 默认 1000。 动画进行中每个笔画之间的间隔时间（以毫秒为单位）。
        daayBetweenLoops: 300, // : 200, // 数值, 默认 2000。 循环动画时每个动画循环之间的时间（以毫秒为单位）。
        autoAnimate: true, // : true,
        stepByStep: true, // : true,
        loopAnimate: true, // : false,
      },
    });
    var stroke2 = cnchar.stroke(str, "order", "name");
    strokaist.value = stroke2;
  } else {
    isCnChar.value = false;
  }
};

onMounted(() => {
  watch(
    () => input.value,
    (value) => {
      setChar(value);
    },
    { immediate: true }
  );
});
</script>

<style lang="less" scoped>
.arco-card {
  text-align: left;
  margin-top: 20px;
}
</style>
