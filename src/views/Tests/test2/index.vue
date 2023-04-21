<!-- <template>
  <div>
    <el-button v-show="$store.state.user.buttons.indexOf('btn.Add3')!=-1" type="primary">添加按钮3</el-button>
  </div>
</template>

<script>
export default {
  name: ''
}
</script>

  <style scoped>

  </style> -->
<template>
  <div id="app">
    <el-form ref="ruleForm" :model="ruleForm" status-icon :rules="rules" label-width="100px" class="demo-ruleForm">
      <el-form-item label="密码" prop="pass">
        <el-input v-model="ruleForm.pass" type="password" autocomplete="off" />
      </el-form-item>
      <el-form-item label="确认密码" prop="checkPass">
        <el-input v-model="ruleForm.checkPass" type="password" autocomplete="off" />
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-input v-model.number="ruleForm.age" />
      </el-form-item>
      <el-form-item>
        <el-button v-loading="loading" type="primary" @click="submitForm">提交</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    var checkAge = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('年龄不能为空'))
      }
      setTimeout(() => {
        if (!Number.isInteger(value)) {
          callback(new Error('请输入数字值'))
        } else {
          if (value < 18) {
            callback(new Error('必须年满18'))
          } else {
            callback()
          }
        }
      }, 1000)
    }
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        if (this.ruleForm.checkPass !== '') {
          // console.log(this.$refs.ruleForm.validateField())
          this.$refs.ruleForm.validateField('checkPass')
        }
      }
    }
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else {
        if (value !== this.ruleForm.pass) {
          callback(new Error('两次密码输入不一致'))
        } else {
          callback()
        }
      }
    }
    return {
      ruleForm: {
        pass: '',
        checkPass: '',
        age: ''
      },
      loading: false,
      rules: {
        pass: [
          { validator: validatePass, trigger: 'blur' }
        ],
        checkPass: [
          { validator: validatePass2, trigger: 'blur' }
        ],
        age: [
          { validator: checkAge, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    submitForm() {
      this.$router.push({ name: 'Test1', params: { id: '89' }})
      const resArr = []
      this.$refs.ruleForm.validateField(['pass', 'checkPass'], (valid) => {
        resArr.push(valid)
      })
      // 所有valid等於空字符串時通過校驗
      if (resArr.every(v => v === '')) {
        // 通過校驗後的操作
        // this.loading = true
        console.log('ppp')
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>
<style lang="sass">
</style>

