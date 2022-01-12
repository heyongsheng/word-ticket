<!--
 * @Author: 贺永胜
 * @Date: 2022-01-12 15:31:53
 * @email: 1378431028@qq.com
 * @LastEditors: 贺永胜
 * @LastEditTime: 2022-01-12 22:15:41
 * @Descripttion: 游戏首页
-->
<template>
  <div id="app">
    <!-- 菜单区 -->
    <div class="menu-wrap"></div>
    <!-- 游戏区 -->
    <div class="game-wrap">
      <!-- 展示区 -->
      <div class="show-wrap">
        <div class="word">{{ currentWord.word }}</div>
        <div class="mean">{{ currentWord.mean }}</div>
      </div>
      <!-- 输入框 -->
      <input type="text" class="word-input" v-model="wordInput" @input="wordCheck" />
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      wordInput: '', // 输入框的值
      wordLibrary: require('@/assets/data/word.json'), // 单词库
      currentWordLibrary: [], // 当前单词库
      currentWord: {}, // 当前单词
    }
  },
  mounted () {
    this.startGame()
  },
  methods: {
    /**
     * @description: 开始游戏
     * @param {*}
     * @return {*}
     */
    startGame () {
      this.currentWordLibrary = [...this.wordLibrary]
      this.drawWord()
    },
    /**
     * @description: 抽取单词
     * @param {*}
     * @return {*}
     */
    drawWord () {
      let dataLength = this.currentWordLibrary.length
      if (dataLength === 0) {
        this.currentWordLibrary = [...this.wordLibrary]
        this.drawWord()
        return
      }
      let randomIndex = Math.floor(Math.random() * dataLength)
      this.currentWord = this.currentWordLibrary.splice(randomIndex, 1)[0]
    },
    /**
     * @description: 单词检测
     * @param {*}
     * @return {*}
     */
    wordCheck () {
      if (this.wordInput === this.currentWord.word) {
        this.drawWord()
        this.wordInput = ''
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url(./assets/img/bg.png) no-repeat;
  background-position: center;
  background-size: cover;
  font-family: PingFangSC-Regular, Microsoft Yahei, sans-serif;
}
/* 展示区 */
.show-wrap {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #fff;
  text-align: center;
  user-select: none;
  padding: 10px 10px;
  background: rgba(0, 0, 0, 0.2);
}
.show-wrap .word {
  font-weight: bold;
  font-size: 60px;
}
.show-wrap .mean {
  font-size: 40px;
}
/* 输入框 */
.word-input {
  position: absolute;
  bottom: 10vh;
  left: 50%;
  transform: translateX(-50%);
  height: 50px;
  width: 200px;
  text-align: center;
  margin: auto;
  font-size: 30px;
  border: none;
  border-bottom: 2px solid #fff;
  background: transparent;
  outline: none;
  color: #fff;
}

/* 手机端样式适配 */
@media screen and (max-width: 500px) {
  .show-wrap .word {
    font-size: 40px;
  }
  .show-wrap .mean {
    font-size: 30px;
  }
}
</style>
