<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片区域 -->
    <el-card class="box-card">
      <div>
        <!-- 搜索与添加区域 -->
        <el-row :gutter="24">
          <el-col :span="9">
            <el-input placeholder="请输入内容">
              <el-button slot="append" icon="el-icon-search"></el-button>
            </el-input>
          </el-col>
          <el-col :span="5">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>

        <!-- 用户列表区域 -->
        <el-table :data="userList" border stripe>
          <el-table-column type="index"></el-table-column>
          <el-table-column label="姓名" prop="username"></el-table-column>
          <el-table-column label="邮箱" prop="email"></el-table-column>
          <el-table-column label="电话" prop="mobile"></el-table-column>
          <el-table-column label="角色" prop="role_name"></el-table-column>
          <el-table-column label="状态">
            <!-- 作用域插槽 -->
            <template slot-scope="scope">
              <!-- {{scope.row.mg_state}} -->
              <el-switch v-model="scope.row.mg_state" @change="userStateChange(scope.row)"></el-switch>
            </template>
          </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="scope">
              <!-- {{scope.row}} -->

              <!-- 修改按钮 -->
              <el-tooltip
                class="item"
                effect="dark"
                content="修改用户"
                placement="top"
                :enterable="false"
              >
                <el-button type="primary" icon="el-icon-edit" circle></el-button>
              </el-tooltip>

              <!-- 删除按钮 -->
              <el-tooltip
                class="item"
                effect="dark"
                content="删除用户"
                placement="top"
                :enterable="false"
              >
                <el-button type="danger" icon="el-icon-delete" circle></el-button>
              </el-tooltip>

              <!-- 收藏按钮 -->
              <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
                <el-button type="warning" icon="el-icon-setting" circle></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页区域 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="queryInfo.pagenum"
          :page-sizes="[1 , 2 , 4]"
          :page-size="queryInfo.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 获取用户列表的参数对象
      queryInfo: {
        query: '',
        // 当前页数
        pagenum: 1,
        // 当前每页显示多少条数据
        pagesize: 3
      },
      userList: [],
      total: 0
    }
  },
  created() {
    this.getUserList()
  },
  methods: {
    async getUserList() {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取用户列表失败')
      }
      // console.log(res)
      this.userList = res.data.users
      this.total = res.data.total
    },
    // 监听 pagesize 改变的事件
    handleSizeChange(newSize) {
      // console.log(newSize)
      this.queryInfo.pagesize = newSize
      // 重新获取数据
      this.getUserList()
    },
    // 监听页码值改变的事件
    handleCurrentChange(newPage) {
      console.log(newPage)
      this.queryInfo.pagenum = newPage
      this.getUserList()
    },
    // 监听 swich 开关状态的改变
    async userStateChange(userInfo) {
      // console.log(userInfo)
      const { data: res } = await this.$http.put(
        `users/${userInfo.id}/state/${userInfo.mg_state}`
      )
      console.log(res)
      if (res.meta.status !== 200) {
        // 失败之后立即取反 返回原来的状态值
        userInfo.mg_state = !userInfo.mg_state
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功!')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
