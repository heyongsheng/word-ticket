<!--
 * @Author: 贺永胜
 * @Date: 2022-01-12 15:31:53
 * @email: 1378431028@qq.com
 * @LastEditors: 贺永胜
 * @LastEditTime: 2022-01-13 01:00:16
 * @Descripttion: 游戏首页
-->
<template>
  <div id="app">
    <!-- 游戏介绍 -->
    <div class="panel-class" v-show="gameStatus === 'desc'">
      <div class="panel-text">赶车啦！</div>
      <div class="panel-desc">
        高铁还有一分钟停止检票，你距离高铁站还有5000米，打出一个单词可以瞬移100米，拼手速时刻到了，快来试试你能否在规定时刻到达高铁站！输入 abandon 开始游戏
      </div>
    </div>
    <!-- 游戏区 -->
    <div class="game-wrap" v-show="gameStatus === 'start'">
      <!-- 进度区 -->
      <div class="progress-wrap">
        <!-- 剩余发车时间 -->
        <div class="progress">
          <p class="progress-text">剩余发车时间：{{currentTime}}s</p>
          <div class="progress-inside" :style="{width: currentTime / time * 100 + '%'}">
            <p class="progress-text">剩余发车时间：{{currentTime}}s</p>
          </div>
        </div>
        <!-- 距离车站距离 -->
        <div class="progress">
          <p class="progress-text">距离车站还有：{{currentDistance}}m</p>
          <div class="progress-inside" :style="{width: currentDistance / distance * 100 + '%'}">
            <p class="progress-text">距离车站还有：{{currentDistance}}m</p>
          </div>
        </div>
      </div>
      <!-- 单词展示区 -->
      <div class="show-wrap">
        <div class="word">{{ currentWord.word }}</div>
        <div class="mean">{{ currentWord.mean }}</div>
      </div>
    </div>
    <!-- 游戏结束面板 -->
    <div class="panel-class" v-show="gameStatus === 'end'">
      <!-- 结果 -->
      <div class="panel-text">
        <p v-show="result === 'success'">成功上车！</p>
        <p v-show="result === 'fail'">上车失败！</p>
      </div>
      <!-- 战绩 -->
      <div class="panel-desc">
        <p v-show="result === 'success'">经过不懈的努力，您在第 <b>{{round}}</b> 轮游戏中就成功上车了，相信什么话语都无法描述您此刻想回家的心情，青山科技在此祝您的旅途一路顺风，同时也祝您身体健康，阖家欢乐，团团圆圆过大家</p>
        <p v-show="result === 'fail'">很遗憾您没赶上车，不过青山科技本着帮人帮到底的诚意，给您提供了第 <b>{{round + 1}}</b> 次机会，你可以输入 <b>again</b> 穿越到 <b>{{time+5}}</b> 秒前，再来一次，希望这次您能成功！加油！</p>
      </div>
    </div>
    <!-- 输入框 -->
    <input ref="wordInput" type="text" class="word-input" v-model="wordInput" @input="wordCheck" placeholder="在此输入" />
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      round: 0,
      time: 60, // 剩余发车时间
      currentTime: 0, // 当前时间
      distance: 5000,
      currentDistance: 0,
      wordInput: '', // 输入框的值
      wordLibrary: require('@/assets/data/word.json'), // 单词库
      currentWordLibrary: [], // 当前单词库
      currentWord: {}, // 当前单词
      gameStatus: 'desc', // 游戏状态
      result: ''
    }
  },
  mounted () {
    // this.startGame()
  },
  methods: {
    /**
     * @description: 开始游戏
     * @param {*}
     * @return {*}
     */
    startGame () {
      // 改变游戏状态
      this.gameStatus = 'start'
      // 重置本轮游戏词库
      this.currentWordLibrary = [...this.wordLibrary]
      // 重置时间
      this.currentTime = this.time
      // 清空输入框
      this.wordInput = ''
      // 重置距离
      this.currentDistance = this.distance
      // 游戏轮数加一
      this.round++
      // 随机获取一个单词
      this.drawWord()
      // 开始检票
      this.ticketCheck()
    },
    /**
     * @description: 开始检票
     * @param {*}
     * @return {*}
     */
    ticketCheck () {
      if (this.currentTime > 0) {
        setTimeout(() => {
          this.currentTime-=10
          this.ticketCheck()
        }, 1000)
      } else {
        // 游戏结束
        this.gameOver()
      }
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
      // 检测关键词
      if (this.wordInput === 'abandon' || this.wordInput === 'again') {
        if (this.gameStatus !== 'start') {
          this.startGame()
        }
      }
      // 判断是否答对
      if (this.wordInput === this.currentWord.word) {
        // 重置输入框
        this.wordInput = ''
        // 距离减100
        this.currentDistance -= 100
        if (this.currentDistance === 0) {
          this.gameOver()
          return
        }
        // 抽取下一个单词
        this.drawWord()
      }
    },
    /**
     * @description: 游戏结束
     * @param {*}
     * @return {*}
     */    
    gameOver () {
      this.$refs.wordInput.blur()
      // 判断胜负
      if (this.currentTime > 0) {
        // 胜利
        this.result = 'success'
      } else {
        // 失败
        this.result = 'fail'
      }
      this.gameStatus = 'end'
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
/* 进度区 */
.progress-wrap {
  position: absolute;
  width: 80%;
  max-width: 400px;
  background: rgba(0, 0, 0, 0.2);
  top: 10vh;
  left: 50%;
  color: #fff;
  transform: translateX(-50%);
  font-size: 14px;
  line-height: 20px;
}
/* 进度条样式 */
.progress {
  width: 100%;
  height: 20px;
  background: #fff;
  color: #24de62;
  border-radius: 20px;
  overflow: hidden;
  padding: 5px;
  position: relative;
}
.progress:first-child {
  margin-bottom: 20px;
}
.progress-inside {
  width: 100%;
  height: 100%;
  background: #24de62;
  border-radius: 20px;
  color: #fff;
  z-index: 3;
  position: relative;
  transition: width 1s linear;
  overflow: hidden;
}
.progress-inside .progress-text {
  position: inherit;
  left: 5px;
}
.progress-text {
  position: absolute;
  left: 10px;
  z-index: 2;
  white-space: nowrap;
}
/* 单词展示区 */
.show-wrap {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #fff;
  text-align: center;
  user-select: none;
  padding: 10px 20px;
  background: rgba(0, 0, 0, 0.2);
}
.show-wrap .word {
  font-weight: bold;
  font-size: 60px;
}
.show-wrap .mean {
  font-size: 40px;
}
/* 游戏结束面板 */
.panel-class {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  max-width: 400px;
  background: rgb(57 0 0 / 40%);
  color: #fff;
  text-align: center;
  user-select: none;
  padding: 10px 20px;
  font-size: 14px;
  line-height: 20px;
}
.panel-text {
  width: 100%;
  position: absolute;
  font-size: 50px;
  line-height: 80px;
  top: -80px;
  color: #f7f74b;
  font-weight: bold;
  font-style: italic;
  text-align: center;
  text-shadow: 2px 2px 1px #ea7b7b;
}
.panel-desc {
  font-size: 20px;
  line-height: 2;
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
.word-input::-webkit-input-placeholder { /* WebKit browsers */
  color: rgb(254, 254, 223);
  font-size: 16px;
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
