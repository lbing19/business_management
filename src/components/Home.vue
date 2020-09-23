<template>
  <el-container class='home_container'>
    <el-header>
      <div class='logtitle'>商城后台管理系统</div>
      <el-button class='logout' @click='logout' type='info'>退出登录</el-button>
    </el-header>
    <el-container>
      <!-- 侧边栏 -->
      <el-aside width='200px'>
        <el-menu
          :default-active='activeNav'
          router
          unique-opened
          background-color='rgb(107, 129, 136)'
          text-color='#fff'
          active-text-color='#ffd04b'
        >
          <el-submenu :index='item.id+""' v-for='item in menuList' :key='item.id'>
            <template slot='title'>
              <i :class='iconObj[item.id]'></i>
              <span>{{item.authName}}</span>
            </template>
            <el-menu-item
              :index='"/"+item2.path'
              v-for='item2 in item.children'
              :key='item2.id'
              @click='saveNavState("/"+item2.path)'
            >
              <template slot='title'>
                <i class='el-icon-menu'></i>
                <span>{{item2.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>
<script>
export default {
  name: 'Home',
  data() {
    return {
      menuList: [],
      iconObj: {
        '125': 'iconfont icon-users',
        '103': 'iconfont icon-tijikongjian',
        '101': 'iconfont icon-shangpin',
        '102': 'iconfont icon-danju',
        '145': 'iconfont icon-baobiao'
      },
      //被激活的导航栏
      activeNav: ''
    }
  },
  created() {
    this.getMenuList()
    this.activeNav = window.sessionStorage.getItem('navstate')
  },
  methods: {
    logout() {
      window.sessionStorage.clear();
      this.$router.push('/login')
    },
    //获取左侧菜单
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
    },
    //保存导航栏点击状态
    saveNavState(index) {
      this.activeNav = index
      window.sessionStorage.setItem('navstate', index)
    }
  },
  components: {

  }
}
</script>
<style scoped>
.home_container {
  height: 100%;
}
.el-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgb(113, 135, 141);
}
.logtitle {
  font-size: 20px;
  color: #fff;
}
.logout {
  height: 60%;
}
.el-aside {
  background-color: rgb(107, 129, 136);
}
.el-aside .el-menu {
  border-right: 0;
}
.el-main {
  background-color: rgb(233, 237, 241);
}
.iconfont {
  margin-right: 10px;
  color: #fff;
}
</style>