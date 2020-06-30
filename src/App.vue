<template>
  <div id="app" class="index-page">
    <div class="img-wrapper">
      <div
        @click="selectHandle(v)"
        class="img-item"
        :class="[
          selectArr.indexOf(v.id) > -1 ? 'actived' : '',
          endArr.indexOf(v.id) > -1 ? 'disabled' : '',
        ]"
        v-for="(v, i) in imgLists"
        :key="i"
      >
        <img :src="require(`@/assets/${v.src}`)" />
        <span>{{ v.name }}</span>
      </div>
    </div>
    <div v-if="isEnd" class="end-wrapper" @click.stop="clickHandle">
      <div v-if="isEnd === 1">
        <div class="laugh"></div>
        <div class="text">{{endArr.length >= 8 ? '哇！太棒了，全部选对了' : '恭喜答对了！'}}</div>
      </div>
      <div v-if="isEnd === 2">
        <div class="cry"></div>
        <div class="text">答错了，再思考一下🤔？</div>
      </div>
    </div>
    <audio ref="congratulations" controls hidden="true" preload="auto">
      <source src="@/assets/congratulations.mp3" />
    </audio>
    <audio ref="disdain" controls hidden="true" preload="auto">
      <source src="@/assets/disdain.mp3" />
    </audio>
    <audio ref="click" controls hidden="true" preload="auto">
      <source src="@/assets/click.mp3" />
    </audio>
  </div>
</template>

<script lang="ts">
/* eslint-disable semi */
import { Component, Vue } from 'vue-property-decorator';
import imgLists from '@/data.ts';

@Component
export default class App extends Vue {
  imgLists = imgLists;

  selectArr: Array<number> = [];

  endArr: Array<number> = [];

  isEnd = 0; // 1-选对，2-选错

  $refs!: {
    congratulations: HTMLAudioElement;
    disdain: HTMLAudioElement;
    click: HTMLAudioElement;
  };

  // eslint-disable-next-line space-before-function-paren
  selectHandle(v: any): void {
    const { selectArr, endArr } = this;
    if (endArr.indexOf(v.id) > -1 || !v.target) {
      return;
    }
    this.$refs.click.play();
    const index = selectArr.indexOf(v.id);
    if (index > -1) {
      this.selectArr.splice(index, 1);
    } else if (selectArr.length) {
      this.selectArr.push(v.id);
      if (this.selectArr[0] === v.target) {
        this.isEnd = 1;
        this.endArr = this.endArr.concat(this.selectArr);
        // 音频
        this.$refs.congratulations.currentTime = 0;
        this.$refs.congratulations.play();
      } else {
        this.isEnd = 2;
        this.$refs.disdain.currentTime = 0;
        this.$refs.disdain.play();
      }
    } else {
      this.selectArr.push(v.id);
    }
  }

  // eslint-disable-next-line space-before-function-paren
  clickHandle() {
    this.selectArr = [];
    this.isEnd = 0;
    this.$refs.disdain.pause();
    this.$refs.congratulations.pause();
  }
}
</script>

<style lang="scss">
html,
body {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.index-page {
  box-sizing: border-box;
  padding: 20px 0;
  overflow: hidden;
  .img-wrapper {
    overflow: hidden;
    width: 750px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    .img-item {
      width: 231px;
      height: 300px;
      box-sizing: border-box;
      margin-top: 20px;
      font-size: 0;
      span {
        display: block;
        width: 100%;
        height: 56px;
        line-height: 56px;
        font-size: 28px;
      }
      img {
        max-width: 100%;
        max-height: 100%;
      }
      &.actived {
        border: 6px solid #ff00ff;
      }
      &.disabled {
        opacity: 0.5;
        -webkit-filter: grayscale(100%);
        -moz-filter: grayscale(100%);
        -ms-filter: grayscale(100%);
        -o-filter: grayscale(100%);
        filter: grayscale(100%);
        filter: gray;
      }
    }
  }
  .end-wrapper {
    background: rgba($color: #000000, $alpha: 0.6);
    position: fixed;
    left: 0px;
    top: 0px;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    .laugh {
      width: 450px;
      height: 450px;
      margin: 0 auto;
      background: url(./assets/laugh.png) no-repeat center;
    }
    .cry {
      width: 450px;
      height: 450px;
      margin: 0 auto;
      background: url(./assets/cry.png) no-repeat center;
    }
    .text {
      width: 100%;
      font-size: 60px;
      font-weight: bold;
      margin-top: 40px;
      color: gold;
    }
  }
}
</style>
