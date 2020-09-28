<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-button type="primary">添加新角色</el-button>
      <el-table border :data="rolelist">
        <el-table-column type="expand">
          <template v-slot="scope">
            <!-- 一级权限 -->
            <el-row
              v-for="(item1, i1) in scope.row.children"
              :key="item1.id"
              class="bdbottom vcenter"
            >
              <el-col :span="5">
                <el-tag>{{ item1.authName }}</el-tag>
                <i class="el-icon-caret-right"></i>
              </el-col>
              <el-col :span="19">
                <!-- 二级三级权限 -->
                <el-row
                  v-for="(item2, i2) in item1.children"
                  :key="item2.id"
                  :class="[i2 === 0 ? '' : 'bdtop', 'vcenter']"
                >
                  <el-col :span="8">
                    <el-tag type="success">{{ item2.authName }}</el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="16">
                    <el-tag
                      type="warning"
                      v-for="(item3, i3) in item2.children"
                      :key="item3.id"
                      closable
                      @close="removeRole(scope.row, item3.id)"
                      >{{ item3.authName }}</el-tag
                    >
                  </el-col>
                </el-row>
              </el-col>
            </el-row>
          </template>
        </el-table-column>
        <el-table-column label="#" type="index"></el-table-column>
        <el-table-column label="角色名称" prop="roleName"></el-table-column>
        <el-table-column label="角色描述" prop="roleDesc"></el-table-column>
        <el-table-column label="操作" width="300px">
          <template v-slot="scope">
            <el-button size="mini" type="primary" icon="el-icon-edit"
              >编辑</el-button
            >
            <el-button size="mini" type="danger" icon="el-icon-delete"
              >删除</el-button
            >
            <el-button
              size="mini"
              type="warning"
              icon="el-icon-setting"
              @click="showRightDialog(scope.row)"
              >分配权限</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <!-- 分配权限dialog -->
    <el-dialog
      title="分配权限"
      :visible.sync="dialogVisible"
      width="50%"
      @close="closeDialog"
    >
      <el-tree
        ref="eltree"
        :data="rightslist"
        :props="defaultProps"
        show-checkbox
        node-key="id"
        default-expand-all
        :default-checked-keys="checkedid"
      ></el-tree>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="allotRights()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
export default {
  name: 'Roles',
  data() {
    return {
      rolelist: [],
      dialogVisible: false,
      rightslist: [],
      defaultProps: {
        children: 'children',
        label: 'authName'
      },
      checkedid: [],
      roleId: ''
    }
  },
  created() {
    this.getRoleList()
  },
  methods: {
    // 获取角色列表数据
    async getRoleList() {
      const { data: res } = await this.$http.get('/roles')
      if (res.meta.status !== 200) return this.$message.error('获取数据失败！')
      this.rolelist = res.data
    },
    // 删除
    async removeRole(role, rightId) {
      const res = await this.$messagebox.confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(error => error)
      if (res !== 'confirm') {
        return this.$message.info('取消删除操作')
      }
      const { data: result } = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
      if (result.meta.status !== 200) { return this.$message.error('删除失败！') }
      this.$message.success('删除成功！')
      this.getRoleList()
    },
    // 分配权限对话框
    async showRightDialog(role) {
      this.roleId = role.id
      // 请求获取所有权限
      const { data: res } = await this.$http.get('/rights/tree')
      if (res.meta.status !== 200) { return this.$message.error('获取数据失败！') }
      this.rightslist = res.data
      this.getLeafKeys(role, this.checkedid)
      this.dialogVisible = true
    },
    // 通过递归，获取三级权限id，保存到checkedid数组中
    getLeafKeys(node, arr) {
      if (!node.children) {
        return arr.push(node.id)
      }
      node.children.forEach(element => {
        this.getLeafKeys(element, arr)
      });
    },
    closeDialog() {
      this.checkedid = []
    },
    // 绑定确定按钮，分配权限
    async allotRights() {
      const keys = [...this.$refs.eltree.getCheckedKeys(), ...this.$refs.eltree.getHalfCheckedKeys()]
      const str = keys.toString()
      const { data: res } = await this.$http.post(`roles/${this.roleId}/rights`, { rids: str })
      if (res.meta.status !== 200) { return this.$message.error('分配权限失败') }
      this.$message.success('分配权限成功')
      this.dialogVisible = false
      this.getRoleList()
    }
  }
}
</script>
<style scoped>
.el-table {
  margin-top: 20px;
}
.el-tag {
  margin: 7px;
}
.bdtop {
  border-top: 1px solid #eee;
}
.bdbottom {
  border-bottom: 1px solid #eee;
}
.vcenter {
  display: flex;
  align-items: center;
}
</style>