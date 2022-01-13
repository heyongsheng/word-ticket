<!--
 * @Author: 贺永胜
 * @Date: 2022-01-12 15:31:53
 * @email: 1378431028@qq.com
 * @LastEditors: 贺永胜
 * @LastEditTime: 2022-01-13 11:59:54
 * @Descripttion: 游戏首页
-->
<template>
  <div id="app">
    <!-- 文章链接 -->
    <a class="help-btn audio-btn" target="_blank" href="https://baidu.com">?</a>
    <!-- 声音控制 -->
    <div class="audio-btn" :class="{active: audioState}" @click="audioState = !audioState">♫</div>

    <!-- 游戏介绍 -->
    <div class="panel-class" v-show="gameStatus === 'desc'">
      <div class="panel-text">青山抢票</div>
      <div class="panel-desc">
        又到春节了，你归心似箭，但还没买到票，青山抢票推出敲单词加速抢票服务，输入正确50个单词才能买票成功，拼手速的时候到了，快冲！<br>
        输入 abandon 开始游戏
      </div>
    </div>
    <!-- 游戏区 -->
    <div class="game-wrap" v-show="gameStatus === 'start'">
      <!-- 进度区 -->
      <div class="progress-wrap">
        <!-- 剩余发车时间 -->
        <div class="progress">
          <p class="progress-text">余票：{{currentTicketCount}}</p>
          <div class="progress-inside" :style="{width: currentTicketCount / ticketCount * 100 + '%'}">
            <p class="progress-text">余票：{{currentTicketCount}}</p>
          </div>
        </div>
        <!-- 距离车站距离 -->
        <div class="progress">
          <p class="progress-text">剩余验证码：{{currentCodeCount}}</p>
          <div class="progress-inside" :style="{width: currentCodeCount / codeCount * 100 + '%'}">
            <p class="progress-text">剩余验证码：{{currentCodeCount}}</p>
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
        <p v-show="result === 'success'">抢票成功！</p>
        <p v-show="result === 'fail'">票已售罄！</p>
      </div>
      <!-- 战绩 -->
      <div class="panel-desc">
        <p v-show="result === 'success'">经过不懈的努力，您在第 <b>{{round}}</b> 轮游戏中就抢到票了，相信什么话语都无法描述您此刻想回家的心情，青山科技在此祝您的旅途一路顺风，同时也祝您身体健康，阖家欢乐，团团圆圆过大年</p>
        <p v-show="result === 'fail'">抢票失败！不过第 <b>{{round + 1}}</b> 轮放票来了，本轮票量增加5张，你可以输入 <b>again</b> 参与本轮抢票，希望这次您能成功！加油！</p>
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
      dingMedia: require('@/assets/audio/ding.wav'),
      successMedia: require('@/assets/audio/success.mp3'),
      failMedia: require('@/assets/audio/fail.mp3'),
      audioState: false,
      bgAudio: new Audio(require('@/assets/audio/bg.mp3')),
      round: 0,
      ticketCount: 60, // 本轮票量
      currentTicketCount: 0, // 当前票量
      codeCount: 50, // 总验证码数
      currentCodeCount: 0, // 剩余验证码数量
      wordInput: '', // 输入框的值
      wordLibrary: require('@/assets/data/word.json'), // 单词库
      currentWordLibrary: [], // 当前单词库
      currentWord: {}, // 当前单词
      gameStatus: 'desc', // 游戏状态
      result: '',
      ticketCheckInterval: null // 检票定时器
    }
  },
  watch: {
    audioState (val) {
      if (val) {
        this.bgAudio.play()
      } else {
        this.bgAudio.pause()
      }
    }
  },
  mounted () {
    // 设置背景音乐属性
    this.bgAudio.loop = true
    // this.startGame()
  },
  methods: {
    /**
     * @description: 开始游戏
     * @param {*}
     * @return {*}
     */
    startGame () {
      // 如果声音开关打开，则播放背景音乐
      if (this.audioState) {
        this.bgAudio.play()
      }
      // 改变游戏状态
      this.gameStatus = 'start'
      // 游戏轮数加一
      this.round++
      // 重置本轮游戏词库
      this.currentWordLibrary = [...this.wordLibrary]
      // 根据轮数设置游戏时间
      this.ticketCount = this.ticketCount + (this.round - 1 ) * 5
      // 重置时间
      this.currentTicketCount = this.ticketCount
      // 清空输入框
      this.wordInput = ''
      // 重置距离
      this.currentCodeCount = this.codeCount
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
      this.ticketCheckInterval = setInterval(() => {
        this.currentTicketCount--
        if (this.currentTicketCount <= 0) {
          this.gameOver()
        }
      }, 1000)
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
        // 播放音效
        this.playAudio(this.dingMedia)
        // 重置输入框
        this.wordInput = ''
        // 验证码数量减一
        this.currentCodeCount-= 10
        if (this.currentCodeCount <= 0) {
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
      if ((navigator.userAgent.match(/(iPhone|iPod|Android|ios|iOS|iPad|Backerry|WebOS|Symbian|Windows Phone|Phone)/i))) {
        this.$refs.wordInput.blur()
      }
      // 暂停背景音乐
      this.bgAudio.pause()
      // 清楚检票定时器
      clearInterval(this.ticketCheckInterval)
      // 判断胜负
      if (this.currentTicketCount > 0) {
        // 更改胜利状态
        this.result = 'success'
        // 播放胜利音效
        this.playAudio(this.successMedia)
      } else {
        // 失败
        this.result = 'fail'
        // 播放失败音效
        this.playAudio(this.failMedia)
      }
      this.gameStatus = 'end'
    },
    playAudio (src) {
      if (this.audioState) {
        const audio = new Audio()
        audio.src = src
        audio.load()
        audio.volume = .5
        audio.play()
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
/* 声音控制 */
.audio-btn {
  color: #fff;
  font-size: 30px;
  width: 50px;
  height: 50px;
  border: 2px solid #fff;
  line-height: 50px;
  text-align: center;
  border-radius: 50%;
  position: absolute;
  right: 20px;
  top: 20px;
  cursor: pointer;
  user-select: none;
}
.audio-btn.active {
  animation: spin 2s linear infinite;
}
/* 帮助中心 */
.help-btn {
  right: 90px;
  text-decoration: none;
}
@keyframes spin {
    from {
        transform: rotate(0deg);
    }
    to {
        transform: rotate(360deg);
    }
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
  color: #ffff71;
  font-weight: bold;
  font-style: italic;
  text-align: center;
  text-shadow: 2px 2px 2px #ff1d1d;
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
