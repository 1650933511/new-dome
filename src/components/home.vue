<template>
<el-container class="app">
  <el-header class="home">
      <div class="logo"></div>
      <div class="title"><h1>电商后台管理系统</h1></div>
      <div class="logout">欢迎你，xxx <a href="javascript:;" @click="logout">退出</a></div>
  </el-header>
  <el-container>
    <el-aside width="200px">
    <el-menu
      router
      unique-opened
      :default-active= '$route.path.slice(1)'
      class="el-menu-vertical-demo"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b">
      <el-submenu :index="menu.path" v-for= "menu in menuList" :key= "menu.id">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>{{menu.authName}}</span>
        </template>
          <el-menu-item :index="itme.path" v-for ="itme in menu.children" :key="itme.id">
            <i class="el-icon-location"></i>
            <span slot="title">{{itme.authName}}</span>
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
  data () {
    return {
      menuList: []
    }
  },
  methods: {
    async logout () {
      try {
        await this.$confirm('你确定要退出吗', '温馨提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        localStorage.removeItem('key')
        this.$router.push('/login')
        this.$message({
          type: 'success',
          message: '退出成功!'
        })
      } catch (error) {
        this.$message({
          type: 'success',
          message: '伦家爱你呦'
        })
      }
    }
  },
  async created () {
    const res = await this.$axios({
      method: 'get',
      url: 'http://localhost:8888/api/private/v1/menus',
      headers: { Authorization: localStorage.getItem('key') }
    })
    const { data, meta } = res.data
    if (meta.status === 200) {
      this.menuList = data
    }
  }
}
</script>
<style lang="less" scoped>
.app {
  .home {
    height: 60px;
    background-color: #b3c1cd;
    display: flex;
    line-height:60px;
    .title {
      flex: 1;
      text-align: center;
      color: #fff;
    }
    .logo,
    .logout {
      width: 180px;
    }
    .logo {
      background: url('../assets/logo.png') no-repeat center center/contain;
    }
  }
  .el-aside {
    background-color: #545c64;
    .el-menu {
      width: 200px;
    }
  }
  .elmain {
background-color: #eaeef1;
  }
}
</style>
