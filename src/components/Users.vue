<template>
  <div class="users">
    <!-- 设置面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>用户列表</el-breadcrumb-item>
</el-breadcrumb>
  <!-- 搜索框 与 添加框 -->
   <el-input placeholder="请输入内容" v-model="query" class="search">
      <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
    </el-input>
    <!-- 添加框按钮 -->
    <el-button type="success" plain @click= "show">添加用户</el-button>
    <el-table :data="users" style="width: 100%">
    <el-table-column prop="username" label="姓名">
    </el-table-column>
    <el-table-column
      prop="email"
      label="邮箱">
    </el-table-column>
    <el-table-column
      prop="mobile"
      label="电话">
    </el-table-column>
    <el-table-column prop="mg_state" label="用户状态">
      <template v-slot= "scope">
        <el-switch @change= "change(scope.row)"
        v-model="scope.row.mg_state"
        active-color="#13ce66"
        inactive-color="#ff4949">
        </el-switch>
      </template>
    </el-table-column>
    <el-table-column
      prop="mobile"
      label="操作">
      <template v-slot= "scope">
        <!-- 点击让修改用户信息 模态框显示 -->
      <el-button type="primary" icon="el-icon-edit" circle plain size="mini" @click= "mini(scope.row.id)"></el-button>
      <el-button type="danger" icon="el-icon-delete" circle plain size="mini" @click= "del(scope.row)">
        <!-- 设置修改 -->
      </el-button>
      <!-- 点击分配显示分配模态框 -->
      <el-button type="success" icon="el-icon-check" round plain size="mini" @click= "alloca(scope.row)">分配角色</el-button>
      </template>
    </el-table-column>
  </el-table>
   <el-pagination
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
      :current-page="pagenum"
      :page-sizes="[2, 4, 6, 8]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
    <!-- 添加模态框 -->
    <el-dialog title="收货地址"  :visible.sync="showadd" width="30%">
     <el-form :model= "form" ref="form" :rules= "rules" label-width="80px">
        <el-form-item label="用户名" prop="username">
        <el-input placeholder="请输入用户名" v-model="form.username"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input placeholder="请输入密码" v-model="form.password"></el-input>
      </el-form-item>
      <el-form-item label="邮箱" prop="emild">
        <el-input placeholder="请输入密码" v-model="form.emild"></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop="mobile">
        <el-input placeholder="请输入手机号" v-model="form.mobile"></el-input>
      </el-form-item>
      </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button  @click="showadd=false">取消</el-button>
    <el-button type="primary" @click= "add">确 定</el-button>
  </div>
</el-dialog>
<!-- 修改的模态框 -->
<el-dialog title="修改信息" :visible.sync="dialogVisible" width="40%">
    <el-form label-position="right" label-width="80px" :model="formamend" :rules= "rule" ref="forms">
      <el-form-item label="用户名" prop= "user">
      <el-tag type="info">{{formamend.name}}</el-tag>
      </el-form-item>
      <el-form-item label="邮箱地址" prop= "emil">
        <el-input v-model="formamend.emild"></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop= "model">
        <el-input v-model="formamend.model"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" @click= "verification">确 定</el-button>
    </span>
</el-dialog>

<!-- 点击分配角色 -->
      <el-dialog title="分配角色" :visible.sync= "allocation" width="30%" :model="allotment">
        <el-form>
          <el-form-item label="用户名" prop= "user">
            <el-tag type="info">{{allotment.name}}</el-tag>
              </el-form-item>
          <el-form-item label="角色列表" prop= "user">
          <el-select placeholder="请选择" v-model="allotment.rids">
              <el-option v-for="item in options" :key="item.id" :value="item.id" :label="item.roleName">
              </el-option>
        </el-select>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="allocation = false">取 消</el-button>
          <el-button type="primary" @click= "alter">确 定</el-button>
        </span>
      </el-dialog>
  </div>
</template>
<script>
export default {
  data () {
    return {
      pagenum: 1,
      pagesize: 2,
      query: '',
      users: [],
      total: 0,
      showadd: false,
      allocation: false,
      form: {
        username: '',
        password: '',
        emild: '',
        mobile: ''
      },
      dialogVisible: false,
      rules: {
        username: [
          { required: true, message: '用户名不能为空', trigger: ['change', 'blur'] },
          { min: 3, max: 9, message: '长度在3-9位', trigger: ['blur', 'change'] }
        ],
        password: [
          { required: true, message: '用户密码不能为空', trigger: ['blur', 'change'] },
          { min: 6, max: 12, message: '长度在6-12位', trigger: ['blur', 'change'] }
        ],
        email: [
          { type: 'email', message: '请输入一个合法的邮箱', trigger: ['blur', 'change'] }
        ],
        mobile: [
          { pattern: /^1[3-9]\d{9}$/, message: '请输入合法的手机号', trigger: ['blur', 'change'] }
        ]
      },
      rule: {
        user: [
          { required: true, message: '用户名不能为空', trigger: ['change', 'blur'] }
        ],
        emil: [
          { type: 'email', message: '请输入一个合法的邮箱', trigger: ['blur', 'change'] }
        ],
        model: [
          { pattern: /^1[3-9]\d{9}$/, message: '请输入合法的手机号', trigger: ['blur', 'change'] }
        ]
      },
      formamend: {
        emild: '',
        model: '',
        name: '',
        id: ''
      },
      options: [{
      }],
      allotment: {
        name: '',
        userid: '',
        rids: ''
      }
    }
  },
  async created () {
    const { data: { data, meta } } = await this.$axios({
      method: 'get',
      url: `http://localhost:8888/api/private/v1/roles/`,
      headers: { Authorization: localStorage.getItem('key') }
    })
    console.log(data, meta)
    this.options = data
    this.fn()
  },
  methods: {
    async change ({ id, mg_state: mgState }) {
      const res = await this.$axios({
        method: 'put',
        url: `http://localhost:8888/api/private/v1/users/${id}/state/${mgState}`,
        headers: { Authorization: localStorage.getItem('key') }
      })
      const { meta } = res.data
      if (meta.status === 200) {
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
        this.fn()
      } else {
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
      }
    },
    async fn () {
      const res = await this.$axios({
        method: 'get',
        headers: { Authorization: localStorage.getItem('key') },
        url: 'http://localhost:8888/api/private/v1/users',
        params: {
          pagenum: this.pagenum,
          pagesize: this.pagesize,
          query: this.query
        }
      })
      const { data, meta } = res.data
      if (meta.status === 200) {
        this.users = data.users
        this.total = data.total
      }
    },
    handleSizeChange (val) {
      this.pagesize = val
      this.fn()
    },
    handleCurrentChange (val) {
      this.pagenum = val
      this.fn()
    },
    async del ({ id }) {
      try {
        await this.$confirm('此操作将永久删除该文件, 是否继续?', '温馨提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        await this.$axios({
          method: 'delete',
          url: `http://localhost:8888/api/private/v1/users/${id}`,
          headers: { Authorization: localStorage.getItem('key') }
        })
        this.fn()
        this.$notify({
          title: '温馨提示',
          message: '删除成功',
          duration: 1000
        })
      } catch (error) {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      }
    },
    search () {
      this.pagenum = 1
      this.fn()
      this.query = ''
    },
    show () {
      this.showadd = true
    },
    async add () {
      const res = await this.$axios({
        method: 'post',
        url: 'http://localhost:8888/api/private/v1/users',
        data: this.form,
        headers: { Authorization: localStorage.getItem('key') }
      })
      const { meta } = res.data
      if (meta.status === 201) {
        this.$message.success(meta.msg)
        this.$refs.form.resetFields()
        this.showadd = false
        this.pagenum = Math.ceil(++this.total / this.pagesize)
        this.getUserList()
      } else {
        this.$message.error(meta.msg)
      }
    },
    async mini (id) {
      this.dialogVisible = true
      const { data: { data, meta } } = await this.$axios({
        method: 'get',
        url: `http://localhost:8888/api/private/v1/users/${id}`,
        headers: { Authorization: localStorage.getItem('key') }

      })
      console.log(data, meta)
      if (meta.status === 200) {
        this.formamend.name = data.username
        this.formamend.emild = data.email
        this.formamend.model = data.mobile
        this.formamend.id = data.id
      }
    },
    async verification () {
      // console.log('11111')
      // this.$refs.forms.validate()
      const { data: { meta } } = await this.$axios({
        method: 'put',
        url: `http://localhost:8888/api/private/v1/users/${this.formamend.id}`,
        headers: { Authorization: localStorage.getItem('key') },
        data: {
          email: this.formamend.emild,
          mobile: this.formamend.model
        }
      })
      // console.log(meta)
      if (meta.status === 200) {
        this.dialogVisible = false
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
        this.fn()
      } else {
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
      }
    },
    async alloca (row) {
      this.allotment.name = row.username
      this.allocation = true
      this.allotment.userid = row.id
      console.log(this.allotment)
      const { data: { data, meta } } = await this.$axios({
        method: 'get',
        url: `http://localhost:8888/api/private/v1/users/${this.allotment.userid}`,
        headers: { Authorization: localStorage.getItem('key') }
      })
      // console.log(data, meta)
      if (meta.status === 200) {
        this.allotment.rids = data.rid
      }
    },
    async alter () {
      // console.log(this.allotment.rids)
      const { data: { meta } } = await this.$axios({
        method: 'put',
        url: `http://localhost:8888/api/private/v1/users/${this.allotment.userid}`,
        data: { rid: this.allotment.rids },
        headers: { Authorization: localStorage.getItem('key') }
      })
      console.log(meta)
      if (meta.status === 200) {
        this.allocation = false
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
        this.fn()
      } else {
        this.$notify({
          title: '温馨提示',
          message: meta.msg,
          duration: 1000
        })
      }
    }
  }
}
</script>

<style lang="less" scoped>
.users {
.el-breadcrumb {
  background-color: #ddd;
  padding: 10px 10px ;
  border-radius: 5px;
}
.search {
  width: 300px;
  margin-top: 20px;
  margin-right: 30px;
}
}
</style>
