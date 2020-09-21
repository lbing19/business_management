<template>
  <div>
    <el-button @click='logout' type='info'>退出登录</el-button>
  </div>
</template>
<script>
export default {
  name: 'Home',
  data() {
    return {
    }
  },
  methods: {
    logout() {
      window.sessionStorage.clear();
      this.$router.push('/login')
    }
  },
  components: {

  }
}
</script>
<style scoped>
</style>