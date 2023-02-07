<!--
 * @Author: MeowKJ
 * @Date: 2023-02-02 19:47:36
 * @LastEditors: MeowKJ ijink@qq.com
 * @LastEditTime: 2023-02-02 20:58:20
 * @FilePath: /ChatMeowc:/Users/ijink/Project/Ai/chatmeowui/src/App.vue
-->
<template>
  <!-- 感谢API提供 拉菲艾拉La_Pluma-->
  <img alt="" src="https://www.adorable0v0.top/api">
  <HomePage/>
</template>

<script>
import HomePage from './components/HomePage.vue'

export default {
  name: 'App',
  components: {
    HomePage
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

img{
  height: 200px;
}
</style>
