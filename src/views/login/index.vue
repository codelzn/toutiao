<template>
  <div class="login-container">
    <!-- 导航栏 -->
    <van-nav-bar title="登录"
                 class="page-nav-bar">
    <van-icon slot="left" name="cross" @click="$router.back()" />
    </van-nav-bar>
    <!-- 登录表单 -->
    <van-form @submit="onSubmit"
              ref="loginForm">
      <van-field v-model="user.mobile"
                 name="mobile"
                 placeholder="请输入手机号"
                 :rules="userFormRules.mobile"
                 type="number"
                 maxlength="11">
        <i slot="left-icon"
           class="toutiao toutiao-shouji"></i>
      </van-field>
      <van-field v-model="user.code"
                 name="code"
                 placeholder="请输入验证码"
                 :rules="userFormRules.code"
                 type="number"
                 maxlength="6">
        <i slot="left-icon"
           class="toutiao toutiao-yanzhengma"></i>
        <template #button>
          <van-count-down v-if="isCountDownShow"
                          :time="1000 * 10"
                          format="ss s"
                          @finish="isCountDownShow = false"/>
          <van-button v-else
                      size="small"
                      type="default"
                      round
                      class="send-sms-btn"
                      @click.stop="onSendSms"
                      native-type="button">发送验证码</van-button>
        </template>
      </van-field>
      <div class="login-btn-wrap">
        <van-button block
                    type="info"
                    native-type="submit"
                    class="login-btn">
          登录
        </van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
import { login, sendSms } from '@/api/user'
export default {
  data () {
    return {
      user: {
        mobile: '13911111111',
        code: '246810'
      },
      userFormRules: {
        mobile: [{
          required: true,
          message: '手机号不能为空'
        }, {
          pattern: /^1[3|5|7|8]\d{9}$/,
          message: '手机号格式错误'
        }],
        code: [{
          required: true,
          message: '验证码不能为空'
        }, {
          pattern: /^\d{6}$/,
          messgae: '验证码格式错误'
        }]
      },
      isCountDownShow: false // 是否展示倒计时
    }
  },
  methods: {
    async onSubmit () {
      // 获取数据
      const user = this.user
      // 表单验证

      this.$toast.loading({
        message: '登录中...',
        forbidClick: true,
        duration: 0 // 持续展示
      })
      // 发起请求
      try {
        const { data } = await login(user)
        this.$store.commit('setUser', data.data)
        this.$toast.success('登录成功')
        this.$router.back()
      } catch (err) {
        if (err.response.status === 400) {
          this.$toast.fail('手机号或验证码错误')
        }
        this.$toast.fail('登录失败')
      }
    },
    async onSendSms () {
      // 校验手机号码
      try {
        await this.$refs.loginForm.validate('mobile')
      } catch (err) {
        return console.log('验证失败', err)
      }
      // 验证通过显示倒计时
      this.isCountDownShow = true
      // 请求发送验证码
      try {
        await sendSms(this.user.mobile)
        this.$toast('发送成功')
      } catch (err) {
        this.isCountDownShow = false
        if (err.response.status === 429) {
          this.$toast('发送太频繁,请稍后重试')
        } else {
          this.$toast('发送失败,请稍后重试')
        }
      }
    }
  },
  created () { }
}
</script>
<style lang="less" scoped>
.login-container {
  .toutiao {
    font-size: 37px;
  }
  .send-sms-btn {
    background-color: #ededed;
    width: 158px;
    height: 46px;
    line-height: 46px;
    font-size: 22px;
    color: #666;
  }
  .login-btn-wrap {
    padding: 53px 33px;
  }
  .login-btn {
    background: #6db4fb;
    border: none;
  }
}
</style>
