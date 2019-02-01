<template>
  <div id="app">
    <!-- 头部 -->
    <app-header :poiInfo = "poiInfo"></app-header>

    <!-- 导航 -->
    <app-nav></app-nav>

    <!-- 内容 -->
    <router-view class="content" />

  </div>
</template>

<script>
import Header from './components/header/Header'
import Nav from './components/nav/Nav'

export default {
  name: 'App',
  components: {
    'app-header': Header,
    'app-nav': Nav
  },
  data () {
    return {
      poiInfo: {}
    }
  },
  created () {
    fetch('/api/goods', {
      method: 'get',
      headers: {
        'Content-type': 'application/json'
      }
    }).then(data => {
      // 此处得到的数据只是一个可读流，需要进行转化
      return data.json()
    }).then(res => {
      // console.log(res)
      if (res.code === 0) {
        this.poiInfo = res.data.poi_info
      }
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
