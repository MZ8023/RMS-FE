<template>
  <div @keyup.enter="login()" class="container">
    <div>
      <img src="~/assets/img/logo.png" class="logo" alt="中铁北京局（天津）公司人力资源管理系统">
      <p class="title">人力资源管理系统</p>
    </div>
    <el-card class="panel">
      <el-form ref="LoginForm" :disabled="LoginForm.disabled" :model="LoginForm" :rules="inputRules" status-icon>
        <el-form-item label="账号" prop="id">
          <el-input v-model="LoginForm.id" />
        </el-form-item>
        <el-form-item label="密码" prop="pass">
          <el-input v-model="LoginForm.pass" show-password />
        </el-form-item>
        <el-form-item class="text-right">
          <el-button @click="login()" type="primary">登录</el-button>
          <el-button @click="resetForm('LoginForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <el-alert v-show="status" :type="status === 'processing' ? 'info' : status" :title="alertTitle" class="alert" show-icon />
  </div>
</template>
<script>
export default {
  data () {
    // const validateId = (rule, value, callback) => {
    //   const exp = /(iec|ies|IEC|IES)+([0-9a-zA-Z]){6}$/
    //   if (value === '') { return callback(new Error('Please input the Inventec ID')) }
    //   if (!exp.test(value)) { return callback(new Error('Wrong ID format.')) }
    //   callback()
    // }
    // const validatePass = (rule, value, callback) => {
    //   if (value.length < 6) {
    //     callback(new Error('Password too short.'))
    //   } else {
    //     callback()
    //   }
    // }
    return {
      alertTitle: '',
      status: '',
      LoginForm: {
        pass: '',
        id: '',
        disabled: false
      },
      inputRules: {
        pass: [{ required: true, message: '密码不能为空', trigger: 'blur' }],
        id: [{ required: true, message: '账号不能为空', trigger: 'blur' }]
      }
    }
  },
  head () {
    return { title: '登录', meta: [{ hid: 'login' }] }
  },
  methods: {
    login () {
      this.$refs.LoginForm.validate(async valid => {
        this.LoginForm.disabled = true
        if (valid) {
          this.status = 'processing'
          this.alertTitle = '拼命登录中...'
          const credential = { user_name: this.LoginForm.id, user_password: this.LoginForm.pass }
          try {
            const data = await this.$store.dispatch('auth/login', credential)
            if (data.code !== 0) {
              this.alertTitle = `恭喜！登陆成功。`
              this.status = 'success'
              this.$router.push('/')
            } else {
              this.status = 'error'
              console.log(data.message)
              this.$message.error('登录失败！' + data.message)
              this.alertTitle = '登录失败！' + data.message
            }
          } catch (error) {
            console.log(error, 'error')
            this.status = 'error'
            this.alertTitle = '服务器错误，请联系管理员。'
          } finally {
            this.LoginForm.disabled = false
          }
        } else {
          this.status = 'error'
          this.alertTitle = '账号或密码错误'
          this.LoginForm.disabled = false
          return false
        }
      })
    },
    resetForm (formName) {
      this.$refs[formName].resetFields()
      this[formName].disabled = false
      this.status = ''
    }
  },
  layout: 'auth'
}
</script>

<style lang="stylus" scoped>
.title
  line-height 40px
  font-size 20px
  font-weight 900
  color #555555
  text-align center
  margin-bottom 10px
.logo
  height 60px
  margin-bottom 10px
.container
  width 100vw
  height 100vh
  display flex
  justify-content center
  align-items center
  flex-direction column
  .panel,
  .alert
    width 400px
    margin 10px auto
</style>
