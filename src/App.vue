<!--
 * @Author: 贺永胜
 * @Date: 2022-01-12 15:31:53
 * @email: 1378431028@qq.com
 * @LastEditors: 贺永胜
 * @LastEditTime: 2022-01-12 17:37:05
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
        <div class="word">abandon</div>
        <div class="mean">放弃</div>
      </div>
      <!-- 输入框 -->
      <input type="text" class="word-input">
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      //
    }
  },
  components: {
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
  padding: 5px 10px;
  background: rgba(0, 0, 0, .5);
}
.show-wrap .word {
  font-weight: bold;
  font-size: 50px;
}
.show-wrap .mean {
  font-size: 30px;
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
</style>
