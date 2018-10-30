<template>
  <div class="row">
    <div class="col-md-4 col-md-offset-4 floating-box">
      <Message :show.sync="msgShow" :type="msgType" :msg="msg"/>

      <div class="panel panel-default">
        <div class="panel-heading">
          <h3 class="panel-title">Please enter User name and Password</h3>
        </div>

        <div class="panel-body" data-validator-form>
          <div class="form-group">
            <label class="control-label">User name</label>
            <input v-model.trim="username" v-validator.required="{ title: 'username' }" type="text" class="form-control" placeholder="User name">
          </div>
          <div class="form-group">
            <label class="control-label">Password</label>
            <input v-model.trim="password" id="password" v-validator.required="{ title: 'password' }" type="password" class="form-control" placeholder="Password">
          </div>
          <br>
          <button @click="login" type="submit" class="btn btn-lg btn-success btn-block">
            <i class="fa fa-btn fa-sign-in"></i> Submit
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      username: '', // 用户名
      password: '', // 密码
      msg: '', // 消息
      msgType: '', // 消息类型
      msgShow: false // 是否显示消息，默认不显示
    }
  },
  methods: {
    login(e) {
      this.$nextTick(() => {
        const target = e.target.type === 'submit' ? e.target : e.target.parentElement

        if (target.canSubmit) {
          this.submit()
        }
      })
    },
    submit() {
        // 表单里的用户信息
      const user = {
        name: this.username,
        password: this.password
      }
      // 仓库的用户信息
      const localUser = this.$store.state.user

      if (localUser) {
        if (localUser.name !== user.name || localUser.password !== user.password) {
          this.showMsg('user name or password are not correct')
        } else {
          this.$store.dispatch('login')
        }
      } else {
        this.showMsg('User not exist')
      }
    },
    showMsg(msg, type = 'warning') {
      this.msg = msg
      this.msgType = type
      this.msgShow = false

      this.$nextTick(() => {
        this.msgShow = true
      })
    }
  }
}
</script>

<style scoped>

</style>