<template>
  <div class="row">
    <div class="col-md-4 col-md-offset-4 floating-box">
        <!--message components-->
        <Message :show.sync="msgShow" :type="msgType" :msg="msg"/>
      <div class="panel panel-default">
        <div class="panel-heading">
          <h3 class="panel-title">Please Sign Up</h3>
        </div>

        <div class="panel-body" data-validator-form>
            <div class="form-group">
                <label class="control-label">User Name</label>
                <input v-model.trim="username" v-validator:input.required="{ regex: /^[a-zA-Z]+\w*\s?\w*$/, error: 'Start with letter' }" type="text" class="form-control" placeholder="User Name">
            </div>
            <div class="form-group">
                <label class="control-label">Password</label>
                <input id="password" v-model.trim="password" v-validator.required="{ regex: /^\w{6,16}$/, error: '6 ~ 16' }" type="password" class="form-control" placeholder="Password">
            </div>
            <div class="form-group">
                <label class="control-label">Comfirm Password</label>
                <input v-model.trim="cpassword" v-validator.required="{ target: '#password' }" type="password" class="form-control" placeholder="Comfirm Password">
            </div>
            <div class="form-group">
                <label class="control-label">Picture</label>
                <input v-model.trim="captcha" v-validator.required="{ title: 'Picture' }" type="text" class="form-control" placeholder="Picture Number">
            </div>
            <div class="thumbnail" title="Press and get new picture" @click="getCaptcha">
                <div class="captcha vcenter" v-html="captchaTpl"></div>
            </div>
            <button type="submit" class="btn btn-lg btn-success btn-block"  @click="register">
                <i class="fa fa-btn fa-sign-in"></i> Sign Up
            </button>
            </div>
      </div>
    </div>
  </div>
</template>

<script>
import createCaptcha from '../../utils/createCaptcha'
import ls from '../../utils/localStorage'

export default {
    
  name: 'Register',
  data() {
    return {
      captchaTpl: '', // 验证码模板
      username: '', // 用户名
      password: '', // 密码
      cpassword: '', // 确认密码
      captcha: '', // 验证码
      msg: '', // 消息
      msgType: '', // 消息类型
      msgShow: false // 是否显示消息，默认不显示
    }
  },
  created() {
    this.getCaptcha()
  },
  methods: {
    getCaptcha() {
      const { tpl, captcha } = createCaptcha(6)

      this.captchaTpl = tpl
      this.localCaptcha = captcha
    },
    //Registe
    register(e) {
      this.$nextTick(() => {
        const target = e.target.type === 'submit' ? e.target : e.target.parentElement

        if (target.canSubmit) {
          this.submit()
        }
      })
    },

    // 向 localStorage 提交数据
    submit() {
    // 检查验证码是否匹配
      if (this.captcha.toUpperCase() !== this.localCaptcha) {
        this.showMsg('picture is not correct')
        // 重新获取验证码
        this.getCaptcha()
      } else {
        // 表单里的用户信息
        const user = {
          name: this.username,
          password: this.password,
          // 根据用户名，从线上返回一张头像
          avatar: `https://api.adorable.io/avatars/200/${this.username}.png`
        }
        const localUser = this.$store.state.user
        // localStorage 的用户信息
        if (localUser) {
            // 检查是否重名
          if (localUser.name === user.name) {
            this.showMsg('User name exist')
          } else {
            this.login(user)
          }
        } else {
          this.login(user)
        }
      }
    },


    // 登陆
    login(user) {
        
      this.$store.dispatch('login', user)// 保存用户信息
      this.showMsg('Sign up successful')
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
.thumbnail { width: 170px; margin-top: 10px; cursor: pointer;}
.thumbnail .captcha { height: 46px; background: #E1E6E8;}
.captcha { font-size: 24px; font-weight: bold; user-select: none;}
</style>