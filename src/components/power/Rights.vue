<template>
  <div>
    <el-breadcrumb separator-class='el-icon-arrow-right'>
      <el-breadcrumb-item :to='{ path: "/home"}'>首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-table border stripe :data='rightslist'>
        <el-table-column label='#' type='index'></el-table-column>
        <el-table-column label='权限名称' prop='authName'></el-table-column>
        <el-table-column label='路径' prop='path'></el-table-column>
        <el-table-column label='权限等级'>
          <template v-slot='scope'>
            <el-tag v-if='scope.row.level == 0'>一级</el-tag>
            <el-tag v-if='scope.row.level == 1'>二级</el-tag>
            <el-tag v-if='scope.row.level == 2'>三级</el-tag>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>
<script>
export default {
  name: 'Rights',
  data() {
    return {
      rightslist: [],
      total: 0,
      currentPage: 1
    }
  },
  created() {
    this.getRights();
  },
  methods: {
    // 获取权限列表
    async getRights() {
      const { data: res } = await this.$http.get('rights/list')
      if (res.meta.status !== 200) return this.$message.error('获取数据失败')
      this.rightslist = res.data
      this.total = res.data.length
    },

  },
}
</script>
<style scoped>
</style>