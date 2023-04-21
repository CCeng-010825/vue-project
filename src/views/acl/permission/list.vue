<template>
  <div>
    <el-table :data="menuPermissionList" style="margin-bottom: 20px;" row-key="id" :expand-row-keys="arr" border>
      <el-table-column
        prop="name"
        label="名称"
      />
      <el-table-column
        prop="code"
        label="权限值"
      />
      <el-table-column
        prop="toCode"
        label="跳转权限值"
      />
      <el-table-column>
        <template slot-scope="{row}">
          <!-- <el-button
            type="primary"
            icon="el-icon-plus"
            size="mini"
            @click="add(row)"
          /> -->
          <HintButton
            type="primary"
            icon="el-icon-plus"
            size="mini"
            :disabled="row.level===4"
            :title="getTitle(row)"
            @click="add(row)"
          />
          <HintButton
            type="primary"
            icon="el-icon-edit"
            size="mini"
            :disabled="row.level===1"
            :title="row.level===4 ? '修改功能' : '修改菜单'"
            @click="edit(row)"
          />
          <HintButton
            type="danger"
            icon="el-icon-delete"
            size="mini"
            :disabled="row.level===1"
            title="删除"
            @click="del(row)"
          />
        </template>

      </el-table-column>
    </el-table>
    <el-dialog
      :visible.sync="dialogPermissionVisible"
      :title="dialogTitle"
      @close="resetData"
    >

      <el-form ref="permission" :model="permission" :rules="permissionRules" label-width="120px">
        <el-form-item v-if="permission.level>2 && !permission.id" label="父级名称">
          <el-input :value="permission.pname" disabled />
        </el-form-item>
        <el-form-item label="名称" prop="name">
          <el-input v-model="permission.name" />
        </el-form-item>

        <el-form-item label="功能权限值" prop="code">
          <el-input v-model="permission.code" />
        </el-form-item>

        <el-form-item v-if="permission.level===4" label="跳转路由权限值" prop="toCode">
          <el-input v-model="permission.toCode" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="resetData">取 消</el-button>
        <el-button type="primary" @click="addOrUpdatePermission">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
// 菜单权限校验的规则
const menuRules = {
  name: [{ required: true, message: '名称必须输入' }],
  code: [{ required: true, message: '权限值必须输入' }]
}

// 按钮功能权限校验的规则
const btnRules = {
  name: [{ required: true, message: '名称必须输入' }],
  code: [{ required: true, trigger: 'blur', message: '功能权限值必须输入' }]
}
export default {
  name: 'PermissionList',

  data() {
    return {
      menuPermissionList: [],
      arr: [],
      dialogPermissionVisible: false,
      // dialogTitle: '',
      permission: {
        level: '',
        name: '',
        code: '',
        toCode: ''
      }
    }
  },
  computed: {
    permissionRules() {
      return this.permission.level === 4 ? btnRules : menuRules
    },
    dialogTitle() {
      // if (this.permission.level > 3) {
      //   return '添加功能'
      // } else {
      //   return this.permission.level === 2 ? '添加一级菜单' : '添加二级菜单'
      // }
      const { id, level } = this.permission
      if (id) {
        return level === 4 ? '修改功能' : '修改菜单'
      } else {
        return level === 4 ? '添加功能' : `添加${level === 2 ? '一级' : '二级'}菜单`
      }
    }
  },
  mounted() {
    this.fetchPermissionList()
  },

  methods: {
    // fetchPermissionList() {
    // //   console.log(this.$API.permission.removePermission())
    //   //   return
    //   console.log(getPermissionList())
    //   getPermissionList().then(res => {
    //     this.tableData = res.data.children
    //     console.log(res)
    //   })
    // }
    async fetchPermissionList() {
      const result = await this.$API.permission.getPermissionList()
      this.menuPermissionList = result.data.children
      this.arr = [this.menuPermissionList[0].id]
    //   this.expandKeys = [this.menuPermissionList[0].id]
    },
    getTitle(row) {
      if (row.level === 1 || row.level === 2) {
        return '添加菜单'
      } else if (row.level === 3) {
        return '添加功能'
      }
    },
    addOrUpdatePermission() {
      this.$refs.permission.validate((valid) => {
        if (valid) {
          this.$API.permission[this.permission.id ? 'updatePermission' : 'addPermission'](this.permission).then(res => {
            this.$message.success(res.message || `${this.permission.name}${this.permission.id ? '更新' : '添加'}成功`)
            this.resetData()
            this.fetchPermissionList()
          }).catch(() => {
            // alert(err)
          })
        }
      })
    },
    resetData() {
      this.dialogPermissionVisible = false
      this.permission = {
        level: '',
        name: '',
        code: '',
        toCode: ''
      }
    },
    add(row) {
      this.dialogPermissionVisible = true
      // Object.assign(this.permission, row)
      console.log(row)
      this.permission.pid = row.id
      this.permission.level = row.level + 1
      this.permission.type = this.permission.level === 4 ? 2 : 1
      this.permission.pname = row.name // 用于显示父名称, 但提交请求时是不需要的

      // 清除校验(必须在界面更新之后)
      this.$nextTick(() => this.$refs.permission.clearValidate())
    },
    edit(row) {
      this.dialogPermissionVisible = true
      this.permission = { ...row }
      this.permission.type = this.permission.level === 4 ? 2 : 1
      this.$nextTick(() => this.$refs.permission.clearValidate())
    },
    del(row) {
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async() => {
        const result = await this.$API.permission.removePermission(row.id)
        this.$message.success(result.message || '删除成功!')
        this.fetchPermissionList()
      }).catch((error) => {
        if (error === 'cancel') {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        }
      })
    }
  }

}
</script>

<style>

</style>
