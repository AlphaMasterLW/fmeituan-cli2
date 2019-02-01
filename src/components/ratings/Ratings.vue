<template>
    <div class="ratings">
      <button @click="goToUrl()">跳转</button>
        <a href="http://www.baidu.com">去百度</a>
    </div>
</template>

<script>
export default {
  methods: {
    goToUrl () {
      window.location.href="http://localhost:8084"
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
