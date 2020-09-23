<template>
  <div class='login_content'>
    <div class='login_box'>
      <div class='avatar_box'>
        <img src='../assets/img/photo.jpg' />
      </div>
      <el-form :model='loginForm' class='user_form' :rules='loginRules' ref='login'>
        <el-form-item class='item_form' prop='username'>
          <el-input
            prefix-icon='iconfont icon-user'
            v-model='loginForm.username'
            placeholder='请输入用户名'
          ></el-input>
        </el-form-item>
        <el-form-item class='item_form' prop='password'>
          <el-input
            type='password'
            prefix-icon='iconfont icon-3702mima'
            v-model='loginForm.password'
            placeholder='请输入密码'
          ></el-input>
        </el-form-item>
        <el-form-item class='btns'>
          <el-button type='primary' @click='login'>提交</el-button>
          <el-button @click='resetForm'>重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Login',
  data() {
    return {
      loginForm: {
        username: '',
        password: ''
      },
      loginRules: {
        username: [
          { required: true, message: '请输入用户名称', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 12, message: '长度在 6 到 12 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {

    //重置表单
    resetForm() {
      this.$refs.login.resetFields()
    },
    login() {
      this.$refs.login.validate(async (valid) => {
        if (!valid) {
          return;
        } else {
          const res = await this.$http.post('login', this.loginForm)
          if (res.data.meta.status !== 200) {
            this.$message.error('登录失败,用户名或密码输入错误！')
          } else {
            this.$message.success('登录成功！')
            //保存token至sessionstorage,页面跳转
            window.sessionStorage.setItem('token', res.data.data.token)
            window.sessionStorage.setItem('username', res.data.data.username)
            this.$router.push('/home')
          }
        }
      })
    }
  },
  components: {

  }
}
</script>
<style scoped>
.login_content {
  background-color: rgb(130, 163, 173);
  height: 100%;
}
.login_box {
  width: 500px;
  height: 350px;
  background-color: #fff;
  border-radius: 5px;
  position: relative;
  left: 50%;
  top: 50%;
  transform: translateY(-50%) translateX(-50%);
}
.avatar_box {
  position: relative;
  left: 50%;
  transform: translate(-50%, -60%);
  height: 160px;
  width: 160px;
  overflow: hidden;
  box-shadow: 0 0 10px #eee;
  border-radius: 50%;
}
.avatar_box img {
  width: 100%;
}
.user_form {
  position: absolute;
  top: 100px;
  left: 40px;
  width: 80%;
}
.item_form {
  padding: 5px 0;
}
.btns {
  display: flex;
  justify-content: center;
  padding-top: 10px;
}
</style>