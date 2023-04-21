<template>
  <div>
    <el-form inline>
      <el-form-item>
        <el-input v-model="tempSearchObj.roleName" placeholder="角色名称" />
      </el-form-item>
      <el-button type="primary" icon="el-icon-search" @click="search">查询</el-button>
      <el-button @click="resetSearch">清空</el-button>
    </el-form>
    <div style="margin-bottom: 20px">
      <!-- 添加与批量添加按钮 -->
      <el-button type="primary" @click="addRole">添 加</el-button>
      <el-button type="danger" :disabled="selectedRoles.length === 0" @click="removeRoles()">批量删除</el-button>
    </div>
    <el-table
      ref="roleTable"
      v-loading="listLoading"
      :data="tableData"
      border
      stripe
      style="width: 960px"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column type="index" label="序号" width="80" />
      <el-table-column label="角色名称">
        <template slot-scope="{row}">
          <template v-if="row.edit">
            <el-input v-model="row.roleName" class="edit-input" size="small" />
            <el-button
              class="cancel-btn"
              size="small"
              icon="el-icon-refresh"
              type="warning"
              @click="cancelEdit(row)"
            >
              取消
            </el-button>
          </template>
          <span v-else>{{ row.roleName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="300" align="center">
        <template slot-scope="{row}">
          <HintButton title="分配权限" size="mini" type="info" icon="el-icon-info" @click="go(row)" />
          <!-- <HintButton
            size="mini"
            type="info"
            icon="el-icon-info"
            title="分配权限"
            @click="$router.push(`/acl/role/auth/${row.id}?roleName=${row.roleName}`)"
          /> -->
          <HintButton
            v-if="row.edit"
            size="mini"
            type="primary"
            icon="el-icon-check"
            title="确定"
            @click="updateRole(row)"
          />
          <HintButton
            v-if="!row.edit"
            size="mini"
            type="primary"
            icon="el-icon-edit"
            title="修改角色"
            @click="row.edit= true"
          />
          <HintButton
            size="mini"
            type="danger"
            icon="el-icon-delete"
            title="删除角色"
            @click="removeRole(row)"
          />
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :current-page="page"
      :total="total"
      :page-size="limit"
      :page-sizes="[5, 10, 20, 30, 40, 50, 100]"
      style="padding: 20px 0;"
      layout="prev, pager, next, jumper, ->, sizes, total"
      @current-change="getRoles"
      @size-change="handleSizeChange"
    />
  </div>
</template>

<script>

export default {
  data() {
    return {
      tempSearchObj: {
        roleName: ''
      },
      selectedRoles: [],
      tableData: [],
      page: 1,
      limit: 5,
      total: 0,
      searchObj: {
        roleName: ''
      },
      listLoading: false,
      originRoleName: ''
    }
  },
  mounted() {
    this.getRoles()
  },
  methods: {
    search() {
      this.searchObj = { ...this.tempSearchObj }
      this.getRoles()
    },
    resetSearch() {
      this.tempSearchObj = {
        roleName: ''
      }
      this.searchObj = {
        roleName: ''
      }
      this.getRoles()
    },
    addRole() {
      this.$prompt('请输入用户角色', '添加用户角色', {
        confirmButtonText: '确定',
        cancelButtonText: '取消'
      }).then(async({ value }) => {
        // console.log(value)
        this.$API.role.save({ roleName: value }).then(result => {
          this.$message.success(result.message || '添加角色成功')
          this.getRoles()
        }).catch(() => {
          this.$message.warning('取消添加')
        })
      })
    },
    removeRoles() {
      // console.log(val)
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async() => {
        // const ids = this.selectedRoles.map(role => role.id)
        this.$API.role.removeRoles(this.selectedRoles).then(res => {
          this.getRoles()
          this.$message({
            type: 'success',
            message: '批量删除成功!'
          })
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    handleSelectionChange(val) {
      // console.log(val)
      this.selectedRoles = [... val.map(item => {
        return item.id
      })]
      // console.log(this.selectedRoles)
    },
    cancelEdit(role) {
      role.edit = false
      role.roleName = role.originRoleName
      this.$message.warning('取消角色修改')
    },
    go(row) {
      this.$router.push({
        path: `/acl/role/auth/${row.id}`,
        query: { roleName: row.roleName }
      })
    },
    updateRole(role) {
      this.$API.role.updateById({ id: role.id, roleName: role.roleName })
        .then(result => {
          this.$message.success(result.message || '更新角色成功!')
          this.getRoles(this.page)
        })
    },
    removeRole(row) {
      this.$confirm('请确定是否删除该角色', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$API.role.removeById(row.id).then(res => {
          this.getRoles(this.tableData.length === 1 ? this.page - 1 : this.page)
          this.$message.success('删除成功')
        })
      }).catch(() => {
        this.$message.info('已取消删除')
      })
    },
    getRoles(page = 1) {
      this.page = page
      this.listLoading = true
      const { limit, searchObj } = this
      this.$API.role.getPageList(page, limit, searchObj).then(res => {
        // console.log(res)
        const tableData = res.data.items
        tableData.map(item => {
          this.$set(item, 'edit', false)
          item.originRoleName = item.roleName
          return item
        })
        this.tableData = tableData
        this.total = res.data.total
      }).finally(() => {
        this.listLoading = false
      })
    },
    handleSizeChange(val) {
      // console.log(val)
      this.limit = val
      this.getRoles()
    }
  }

}
</script>

<style scoped>
.edit-input {
  padding-right: 100px;
}
.cancel-btn {
  position: absolute;
  right: 15px;
  top: 10px;
}
</style>
