<template>
  <div>欢迎登录！ {{username}}</div>
</template>
<script>
export default {
  data() {
    return {
      username: ''
    }
  },
  created() {
    this.username = window.sessionStorage.getItem('username')
  }
}
</script>
<style scoped>
</style>