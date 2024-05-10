<template>
  <div class="game-wrap">
    <div class="top">
      <h1>{{arr.length}}</h1>
      <span class="chance" v-show="failTimes>0">{{failTimes}}</span>
    </div>
    <div class="middle" @click="tap">
      <div v-for="(item,i) in arr" :key="i" class="item" :style="{background:item.color}" :data-special="item.isSpecial"></div>
    </div>
    <div class="bot">
      <div class="desc" v-if="index===2">请点击相异的颜色，开始游戏</div>
      <div class="desc" v-else @click="reset">点我重新开始</div>
      <div class="creator">Developed by JasonBai with Vue@3.2.9</div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";
import randomColor from "../utils/index";

// 成功音效
let successAudio = new Audio();
successAudio.src = require("../assets/sounds/success.mp3");

// 失败音效
let failAudio = new Audio();
failAudio.src = require("../assets/sounds/fail.mp3");

// 猴子音效
let monkeyAudio = new Audio();
monkeyAudio.src = require("../assets/sounds/monkey.mp3");

// 无敌音效
let secretAudio = new Audio();
secretAudio.src = require("../assets/sounds/secret.mp3");

// 方块儿基数
let index = ref(2);
let failTimes = ref(3);

// 单个方块儿的宽度
let width = computed(() => {
  return (1 / index.value) * 100 + "%";
});

// 构建生成方块数组
const arr = computed(() => {
  let _arr = [];
  let len = Math.pow(index.value, 2); // 求平方数
  let randomIndex = parseInt(Math.random() * len); // 生成随机数
  let color = randomColor();
  for (let i = 0; i < len; i++) {
    _arr.push({
      color,
      isSpecial: randomIndex === i,
    });
  }
  return _arr;
});

// 监听index，播放彩蛋音乐
watch(index, (n, o) => {
  if (n === 18) {
    monkeyAudio.play();
  } else if (n === 22) {
    secretAudio.play();
  }
});

// 点击方块儿事件
function tap(e) {
  if (eval(e.target.dataset.special)) {
    successAudio.play();
    index.value++;
  } else if (index.value > 2) {
    failAudio.play();
    failTimes.value--;
    if (failTimes.value < 0) {
      alert("Game Over");
      reset();
    }
  }
}

// 重置游戏
function reset() {
  successAudio.play();
  index.value = 2;
  failTimes.value = 3;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.game-wrap {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  background: #ecf0f1;
  flex-direction: column;
  .top {
    flex-grow: 1;
    display: flex;
    align-items: center;
    margin: -30px 0;
    h1 {
      font-size: 50px;
      margin: 0;
      color: #34495e;
      font-weight: normal;
    }
    .chance {
      position: absolute;
      left: 30px;
      top: 20px;
      font-size: 18px;
      color: #999;
    }
  }
  .middle {
    width: 100%;
    // aspect-ratio: 1/1; // 部分手机不兼容
    height: 100vw;
    flex-shrink: 0;
    display: flex;
    flex-wrap: wrap;
    padding: 3px;
    background: #fff;
    box-sizing: border-box;
    border-radius: 5px;
    .item {
      box-sizing: border-box;
      margin: 3px;
      width: calc(v-bind(width) - 6px);
      flex-shrink: 0;
      border-radius: 5px;
    }

    .item[data-special="true"] {
      filter: brightness(1.8) hue-rotate(25deg);
    }
  }
  .bot {
    display: flex;
    flex-grow: 1;
    flex-direction: column;
    justify-content: space-around;
    .desc {
      font-size: 14px;
      color: #7f8c8d;
    }
    .creator {
      color: #7f8c8d;
      font-size: 12px;
      display: block;
    }
  }
}
</style>
