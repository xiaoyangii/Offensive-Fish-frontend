<template>
  <div class="cgpwd">
    <img src="@/assets/images/login1.png" class="img1">
    <img src="@/assets/images/login.png" class="img2">
    <div class="cgpwd_wrap">
      <div class="cgpwd_wrap_head">
        <div class="cgpwd_wrap_head_title">注册</div>
      </div>
      <div class="cgpwd_wrap_body">
        <div class="cgpwd_form">
          <div class="cgpwd_form_container">
            <label for="account" class="cgpwd_form_container_acclb">账号:</label>
            <input type="tel" id="account" class="cgpwd_form_container_acc" v-model="userName" placeholder="请输入11位手机号">
          </div>
          <div class="cgpwd_form_container">
            <label for="password" class="cgpwd_form_container_pwdlb">密码:</label>
            <input type="password" id="password" class="cgpwd_form_container_pwd" v-model="passwd" placeholder="请输入6-12位密码">
          </div>
          <div class="cgpwd_form_container">
            <label for="repassword" class="cgpwd_form_container_pwdlb">再次确认:</label>
            <input type="password" id="repassword" class="cgpwd_form_container_pwd re" v-model="repasswd" placeholder="请再次输入密码">
          </div>
          <div class="cgpwd_form_container">
            <label for="code" class="cgpwd_form_container_codelb">验证码:</label>
            <input type="tel" id="code" class="cgpwd_form_container_code" v-model="validate" placeholder="请输入6位验证码">
            <button @click="getCode()" class="sendmsg">
              {{ second === totalSecond ? '获取验证码' : second + '秒后重新发送'}}
            </button>
          </div>
        </div>
        <div class="cgpwd_btn">
          <button class="cgpwd_btn_cgpwd" @click="changePwd()">修改</button>
          <button class="cgpwd_btn_back" @click="$router.go(-1)">返回</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { codeForgetPassword, getMsgCode } from '@/api/login.js'
import { validChg, validTel } from '@/utils/validate.js'
export default {
  name: 'changePwd',
  components: {},
  data () {
    return {
      passwd: '',
      userName: '',
      repasswd: '',
      validate: '',
      verifyCode: '',
      totalSecond: 60, // 总秒数
      second: 60, // 当前秒数，开定时器对 second--
      timer: null, // 定时器 id
    }
  },
  methods: {
    async changePwd() {
      if (!validChg(this.userName, this.passwd, this.repasswd, this.validate)) return
      await codeForgetPassword(this.userName, this.validate, this.passwd, this.verifyCode)
      .then(res => {
        if(res.data.msg == '验证码输入错误!') {
          this.$message.error('验证码输入错误')
        } else if(res.data.msg == '账号输入错误!') {
          this.$message.error('账号输入错误!')
        } else {
          this.$message.success('修改成功')
          this.$router.push('/login')
        }
      })
      .catch(err => {
        this.$message.error('修改失败')
      })
    },
    async getCode() {
      if (!validTel(this.userName)) {
        return
      }
      // 当前目前没有定时器开着，且 totalSecond 和 second 一致 (秒数归位) 才可以倒计时
      if (!this.timer && this.second === this.totalSecond) {
        await getMsgCode(this.userName)
        .then(res => {
          this.$message.success('验证码已发送')
          // 开启倒计时
          this.verifyCode = res.headers['content-disposition'].split(';')[1].split('=')[1]
          this.timer = setInterval(() => {
            this.second--
            if (this.second <= 0) {
              clearInterval(this.timer)
              this.timer = null // 重置定时器 id
              this.second = this.totalSecond // 归位
            }
          }, 1000)
        })
        .catch(err => {
          this.$message.error('验证码发送失败')
        })
      }
    }
  }
}
</script>
<style scoped lang='less'>
.cgpwd {
  position: relative;
  width: 100vw;
  height: 60vh;
  z-index: 1;
  padding-left: 30vw;
}
.img1 {
  position: absolute;
  bottom: 0;
  right: 2vw;
  // width: 100vw;
  height: 50vh;
  z-index: -1;
}
.img2 {
  position: absolute;
  bottom: 9.4vh;
  right: 8.3vw;
  height: 50vh;
  scale: 1.05;
}
.cgpwd_wrap {
  width: 40vw;
  height: 90%;
  background-color: #12A8DF;
  border-radius: 2vw;
  z-index: 100;
  box-shadow: 1vw 2vh 0.1vw #A4ADB3;
  &_head {
    height: 15%;
    width: 100%;
    text-align: center;
    &_title {
      padding-top: 1vh;
      .pxfont(40);
      color: #101010;
      font-weight: 550;
    }
  }
  &_body {
    height: 85%;
    width: 100%;
    border-bottom-left-radius: 2vw;
    border-bottom-right-radius: 2vw;
  }
}
.cgpwd_form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 75%;
  width: 40vw;
  &_container {
    display: flex;
    position: relative;
    flex-direction: row;
    align-items: center;
    height: 25%;
    width: 40vw;
    padding-left: 1vw;
    &_acclb, &_pwdlb, &_nicklb, &_codelb {
      .pxfont(40);
      font-weight: 550;
    }
    &_acc, &_pwd, &_nick, &_code {
      margin-left: 1vw;
      height: 70%;
      width: 82%;
      border-radius: 1vw;
      .pxfont(30);
      padding-left: 1.5vw;
    }
    .re {
      width: 71%;
    }
    &_nick, &_code {
      width: 76.5%;
    }
    .sendmsg {
      position: absolute;
      right: 7%;
      border: none;
      .pxfont(20);
      color: #010101;
      background-color: transparent;
    }
  }
}
.cgpwd_btn {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  height: 25%;
  width: 40vw;
  border-bottom-left-radius: 2vw;
  border-bottom-right-radius: 2vw;
  &_cgpwd, &_back {
    height: 7vh;
    width: 8vw;
    border-radius: 2vw;
    background-color: #056DA6;
    .pxfont(40);
    .pxvw(letter-spacing, 2);
    font-weight: 600;
    box-shadow: 0 1vh 0.1vw #333333;
    &:hover {
      transition: all 0.25s linear;
      transform: scale(1.05);
    }
  }
  &_cgpwd {
    margin-right: 2vw;
  }
  &_back {
    margin-left: 5vw;
  }
}
</style>
