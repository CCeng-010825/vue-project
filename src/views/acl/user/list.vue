<template>
  <div class="app-container">
    <el-form inline>
      <el-form-item>
        <el-input v-model="searchObj.username" placeholder="用户名" />
      </el-form-item>
      <!-- 查询与情况的按钮 -->
      <el-button type="primary" icon="el-icon-search" @click="search">查询</el-button>
      <el-button type="default" @click="resetSearch">清空</el-button>
    </el-form>
    <div style="margin-bottom: 20px">
      <!-- 添加与批量添加按钮 -->
      <el-button type="primary" @click="showAddUser">添 加</el-button>
      <el-button
        type="danger"
        :disabled="selectedIds.length===0"
        @click="revomveUsers"
      >批量删除</el-button>
    </div>
    <el-table
      v-loading="listLoading"
      border
      stripe
      :data="users"
      @selection-change="handleSelectionChange"
    >

      <el-table-column
        type="selection"
        width="55"
      />

      <el-table-column
        type="index"
        label="序号"
        width="80"
        align="center"
      />

      <el-table-column prop="username" label="用户名" width="150" />
      <el-table-column prop="nickName" label="用户昵称" />
      <el-table-column prop="roleName" label="权限列表" />

      <el-table-column prop="gmtCreate" label="创建时间" width="180" />
      <el-table-column prop="gmtModified" label="更新时间" width="180" />

      <el-table-column label="操作" width="230" align="center">
        <template slot-scope="{row}">
          <HintButton
            type="info"
            size="mini"
            icon="el-icon-user-solid"
            title="分配角色"
            @click="showAssignRole(row)"
          />
          <HintButton
            type="primary"
            size="mini"
            icon="el-icon-edit"
            title="修改用户"
            @click="showUpdateUser(row)"
          />
          <el-popconfirm :title="`确定删除 ${row.username} 吗?`" @onConfirm="removeUser(row.id)">
            <HintButton
              slot="reference"
              style="margin-left:10px"
              type="danger"
              size="mini"
              icon="el-icon-delete"
              title="删除用户"
            />
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :current-page="page"
      :total="total"
      :page-size="limit"
      :page-sizes="[3, 10, 20, 30, 40, 50, 100]"
      style="padding: 20px 0;"
      layout="prev, pager, next, jumper, ->, sizes, total"
      @current-change="getUsers"
      @size-change="handleSizeChange"
    />
    <el-dialog :title="user.id ? '修改用户' : '添加用户'" :visible.sync="dialogUserVisible">
      <el-form ref="userForm" :model="user" :rules="userRules" label-width="120px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="user.username" />
        </el-form-item>
        <el-form-item label="用户昵称">
          <el-input v-model="user.nickName" />
        </el-form-item>

        <el-form-item v-if="!user.id" label="用户密码" prop="password">
          <el-input v-model="user.password" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button :loading="loading" type="primary" @click="addOrUpdate">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="设置角色" :visible.sync="dialogRoleVisible" :before-close="resetRoleData">
      <el-form label-width="80px">
        <el-form-item label="用户名">
          <el-input disabled :value="user.username" />
        </el-form-item>

        <el-form-item label="角色列表">
          <el-checkbox v-model="checkAll" :indeterminate="isIndeterminate" @change="handleCheckAllChange">全选</el-checkbox>
          <div style="margin: 15px 0;" />
          <el-checkbox-group v-model="userRoleIds" @change="handleCheckedChange">
            <el-checkbox v-for="role in allRoles" :key="role.id" :label="role.id">{{ role.roleName }}</el-checkbox>
          </el-checkbox-group>
        </el-form-item>
      </el-form>

      <div slot="footer">
        <el-button :loading="loading" type="primary" @click="assignRole">保存</el-button>
        <el-button @click="resetRoleData">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tempSearchObj: {
        username: ''
      },
      searchObj: { // 包含请求搜索条件数据的对象
        username: ''
      },
      user: {
        username: '',
        password: '',
        nickName: '',
        id: ''
      },
      selectedIds: [],
      loading: false,
      isIndeterminate: false,
      checkAll: false, // 是否全选
      dialogRoleVisible: false, // 是否显示角色Dialog
      allRoles: [], // 所有角色列表
      userRoleIds: [], // 用户的角色ID的列表
      dialogUserVisible: false,
      users: [],
      listLoading: false,
      page: 1, // 当前页码
      limit: 3, // 每页数量
      total: 0, // 总数量
      //   user: {}, // 当前要操作的user
      userRules: { // 用户添加/修改表单的校验规则
        username: [
          { required: true, message: '用户名必须输入' },
          { min: 4, message: '用户名不能小于4位' }
        ],
        password: [
          { required: true, validator: this.validatePassword }
        ]
      }
    }
  },
  mounted() {
    this.getUsers()
  },
  methods: {
    validatePassword(rule, value, callback) {
      if (!value) {
        callback('密码必须输入')
      } else if (!value || value.length < 6) {
        callback('密码不能小于6位')
      } else {
        callback()
      }
    },
    search() {
      // console.log(this.tempSearchObj.username)
      this.getUsers()
    },
    resetSearch() {
      this.searchObj = {
        username: ''
      }
      this.getUsers()
    },
    showAddUser() {
      this.user = {}
      this.dialogUserVisible = true
      this.$nextTick(() => {
        this.$refs.userForm.clearValidate()
        // console.log('999')
      })
    },
    revomveUsers() {
      this.$confirm('确定删除吗？', '提示', { confirmButtonText: '确定', cancelButtonText: '取消', type: 'warning' }).then(() => {
        this.$API.user.removeUsers(this.selectedIds).then(res => {
          this.$message.success('删除成功')
          this.getUsers()
        })
      }).catch(() => {
        this.$message.info('取消删除')
      })
    },
    handleSelectionChange(val) {
      // console.log(val)
      this.selectedIds = val.map(item => item.id)
      // console.log(this.selectedIds)
    },
    showAssignRole(row) {
      // console.log(row)
      this.user = row
      this.dialogRoleVisible = true
      this.getRoles()
    },
    getRoles() {
      this.$API.user.getRoles(this.user.id).then(res => {
        this.allRoles = res.data.allRolesList
        this.userRoleIds = res.data.assignRoles.map(item => item.id)
        this.checkAll = this.allRoles.length === this.userRoleIds.length
        this.isIndeterminate = this.userRoleIds.length > 0 && this.userRoleIds.length < this.allRoles.length
      })
    },
    showUpdateUser(row) {
      this.dialogUserVisible = true
      this.user.username = row.username
      this.user.nickName = row.nickName
      this.user.password = row.password
      this.user.id = row.id
      this.$nextTick(() => {
        this.$refs.userForm.clearValidate()
      })
    },
    removeUser(id) {
      this.$API.user.removeById(id).then(() => {
        this.$message.success('删除成功')
        this.getUsers(this.users.length === 1 ? this.page - 1 : this.page)
      })
    },
    getUsers(page = 1) {
      this.page = page
      const limit = this.limit
      const searchObj = this.searchObj
      this.listLoading = true
      //   console.log(this.$API)
      this.$API.user.getPageList(page, limit, searchObj).then(res => {
        // console.log(res)
        const items = res.data.items
        this.users = items.filter(item => item.username !== 'admin')
        this.listLoading = false
        this.total = res.data.total - 1
        this.selectedIds = []
      })
    },
    handleSizeChange(value) {
    //   console.log(value)
      this.limit = value
      this.getUsers()
    },
    cancel() {
      this.dialogUserVisible = false
      this.user = {}
    },
    addOrUpdate() {
      this.$refs.userForm.validate((valid) => {
        if (valid) {
          console.log(this.user)
          this.$API.user[this.user.id ? 'update' : 'add'](this.user).then(res => {
            this.loading = false
            this.$message.success('保存成功!')
            this.getUsers(this.user.id ? this.page : 1)
            this.user = {}
            this.dialogUserVisible = false
          })
        }
      })
    },
    assignRole() {
      const userId = this.user.id
      const roleIds = this.userRoleIds.join(',')
      this.loading = true
      this.$API.user.assignRoles(userId, roleIds).then(res => {
        this.$message.success(res.message || '分配角色成功')
        this.loading = false
        // this.dialogRoleVisible = false
        this.resetRoleData()
        window.location.reload()
        console.log(this.$store.getters.name, this.user)
      })
    },
    resetRoleData() {
      this.dialogRoleVisible = false
      this.allRoles = []
      this.userRoleIds = []
      this.isIndeterminate = false
      this.checkAll = false
    },
    handleCheckAllChange(val) {
      this.userRoleIds = val ? this.allRoles.map(item => item.id) : []
      this.isIndeterminate = false
    },
    handleCheckedChange(val) {
      // console.log(val)
      this.userRoleIds = val
      this.checkAll = this.userRoleIds.length === this.allRoles.length
      this.isIndeterminate = this.userRoleIds.length > 0 && this.userRoleIds.length < this.allRoles.length
    }
  }

}
</script>

<style>

</style>
