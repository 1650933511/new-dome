<template>
  <div class="login">
    <el-form ref="form" :model="form" label-width="80px" :rules= "router" status-icon>
      <img src="../assets/avatar.8fae9117.jpg" alt="">
  <el-form-item label="用户名" prop= "username">
    <el-input v-model="form.username"></el-input>
  </el-form-item>
  <el-form-item label="密码" prop= "password">
    <el-input v-model="form.password"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click= "onSubmit">登录</el-button>
    <el-button @click= "fn">注销</el-button>
  </el-form-item>
</el-form>
  </div>
</template>
<script>
export default {
  data () {
    return {
      form: {
        username: '',
        password: ''

      },
      router: {
        username: [
          { required: true, message: '请输入用户名', trigger: ['change', 'blur'] },
          { min: 3, max: 9, message: '长度在 3 到 9 个字符', trigger: ['blur', 'change'] }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: ['change', 'blur'] },
          { min: 3, max: 12, message: '长度在 3 到 12 个字符', trigger: ['blur', 'change'] }
        ]
      }
    }
  },
  methods: {
    fn () {
      this.$refs.form.resetFields()
      this.$notify({
        title: '温馨提示',
        message: '注销成功！么么哒▽',
        duration: 1500
      })
    },
    onSubmit () {
      this.$refs.form.validate(async valid => {
        if (valid) {
          const res = await this.$axios({
            method: 'post',
            url: `http://localhost:8888/api/private/v1/login`,
            data: this.form
          })
          if (res.data.meta.status === 200) {
            localStorage.setItem('key', res.data.data.token)
            this.$router.push('/home')
            this.$message({
              showClose: true,
              message: res.data.meta.msg,
              type: 'warning'
            })
          } else {
            this.$notify({
              title: '温馨提示',
              message: res.data.meta.msg,
              duration: 1000
            })
          }
        }
      })
    }
  }
}
</script>

<style lang = "less" >
.login {
height: 100%;
background-color: #2d434c;
overflow: hidden;
.el-form {
  position: relative;

  border-radius: 20px;
  margin: 200px auto;
  width: 400px;
  background-color: #fff;
  padding: 65px 40px 15px;
  .el-button:last-child {
    margin-left: 80px;
  }
}
img {
  position: absolute;
  left: 50%;
  top: -70px;
transform: translateX(-50%);
border-radius: 50%;
border: 10px solid #fff;

}
}
</style>
