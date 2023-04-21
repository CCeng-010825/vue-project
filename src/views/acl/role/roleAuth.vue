<template>
  <div>
    <el-input disabled :value="$route.query.roleName" />
    <el-tree
      ref="tree"
      style="margin: 20px 0"
      :data="allPermissions"
      node-key="id"
      show-checkbox
      default-expand-all
      :default-checked-keys="arr"
      :props="defaultProps"
    />
    <el-button :loading="loading" type="primary" @click="save">保存</el-button>
    <el-button @click="$router.replace({name: 'Role'})">取消</el-button>
  </div>
</template>

<script>

export default {
  data() {
    return {
      loading: false,
      allPermissions: [],
      selectId: [],
      arr: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  created() {
    this.init()
  },

  methods: {
    init() {
      const roleId = this.$route.params.id
      this.getPermissions(roleId)
    },
    getPermissions(roleId) {
      this.$API.permission.toAssign(roleId).then(res => {
        // console.log(res)
        const allPermissions = res.data.children
        this.allPermissions = allPermissions
        const checkedIds = this.getSelect(allPermissions)
        // console.log(checkedIds)
        this.$refs.tree.setCheckedKeys(checkedIds)
      })
    },
    getSelect(value, arr = []) {
      // return value.map(item => {
      //   if (item.select && item.level === 4) {
      //     this.selectId.push(item.id)
      //   } else if (item.children) {
      //     this.getSelect(item.children)
      //   }
      //   // console.log(this.selectId)
      //   return this.selectId
      // })
      return value.reduce((pre, item) => {
        if (item.level === 4 && item.select) {
          pre.push(item.id)
        } else if (item.children) {
          this.getSelect(item.children, arr)
        }
        // console.log(pre, 'pre')
        // console.log(arr, 'arr')
        return pre
      }, arr)
    },
    save() {
      var ids = this.$refs.tree.getCheckedKeys().join(',')
      // console.log(ids)
      this.loading = true
      this.$API.permission.doAssign(this.$route.params.id, ids).then(result => {
        this.loading = false
        this.$message.success(result.$message || '分配权限成功')
        // const roleName = this.$route.query.roleName
        // const name = this.$store.getters.name
        // console.log(this.$store.getters.roles)
        // return
        this.$router.push({
          path: '/acl/role/list'
        })
      })
    }
  }

}
</script>

<style>

</style>
