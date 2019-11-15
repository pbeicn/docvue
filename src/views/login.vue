<template>
  <div class="h1">
    <div class="login_h1">
      <div class="login_h2">
        <img src="@/assets/l3.png" width="90%" />
      </div>
      <div class="login_p1">
        <div class="login_h3">欢迎登录就诊问诊</div>
        <div class="login_h4">
          <div for="username">用户名</div>
          <input id="username" type="text" placeholder="请输入用户名" v-model="logininfo.username" />
          <br />
          <div for="password" style="margin-top:20px">密码</div>
          <input id="password" type="password" placeholder="请输入密码" v-model="logininfo.password" />
        </div>
        <div class="login_h6">
          <div class="login_h5">
            <div class="login_h8" @click="toLogin">登录</div>
            <div class="h8">
              还没有账号，点击
              <router-link to="reg">注册</router-link>一下
            </div>
          </div>
          <div class="login_h7"></div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      name: "ss",
      username: "",
      password: "",
      tipinfo: "",
      logininfo: {
        grant_type: "password",
        client_id: "practice",
        client_secret: "neuqsoft2019",
        username: "513436200010056380",
        password: ""
      }
    };
  },
  methods: {
    toLogin() {
      sessionStorage.clear();
      this.$ajax
        .post("/oauth/token", this.$qs.stringify(this.logininfo))
        .then(res => {
          window.console.log(res);
          sessionStorage.setItem(
            "access_token",
            "bearer" + res.data.access_token
          );
          this.$router.push("/main");
        })
        .catch(res => {
          window.console.log(res);
        });
    }
  }
};
</script>
<style lang="less" scoped>
.bg {
  background-color: aquamarine;
}
.bg1 {
  background-color: sandybrown;
}
.h1 {
  background-image: url("../assets/l1.png");
  background-size: 100% 100%;
  height: 100%;
  position: fixed;
  width: 100%;
  display: flex;
  flex-direction: row; /* 子元素横向排列 */
  justify-content: center; /* 相对父元素水平居中 */
  align-items: center; /*  子元素相对父元素垂直居中 */
}
.login_h1 {
  width: 1000px;
  height: 80%;
  background: rgba(255, 255, 255, 1);
  box-shadow: 0px 3px 18px 0px rgba(21, 24, 113, 1);
  border-radius: 18px;
  display: flex;
}
.login_p1 {
  width: 510px;
  height: 100%;
  padding-top: 5%;
  padding-left: 3%;
  text-align: left;
  display: flex;
  flex-direction: column;
}
.login_h2 {
  width: 490px;
  height: 100%;
  background: linear-gradient(
    147deg,
    rgba(255, 255, 255, 1) 0%,
    rgba(199, 226, 254, 1) 100%,
    rgba(200, 227, 254, 1) 100%
  );
  border-radius: 18px 0px 0px 18px;
  display: flex;
  flex-direction: row; /* 子元素横向排列 */
  justify-content: center; /* 相对父元素水平居中 */
  align-items: center; /*  子元素相对父元素垂直居中 */
}
.login_h3 {
  height: 86px;
  font-size: 30px;
  font-family: PingFangSC-Semibold, PingFang SC;
  font-weight: 600;
  color: rgba(51, 51, 51, 1);
}
.login_h4 {
  font-size: 20px;
  font-family: PingFangSC-Semibold, PingFang SC;
  font-weight: 600;
  color: rgba(51, 51, 51, 1);
  line-height: 40px;
  margin-top: 25px;
}
.login_h5 {
  background-image: url("../assets/l2.png");
  background-repeat: no-repeat;
  background-size: 100% 100%;
  -moz-background-size: 100% 100%;
  width: 400px;
  height: 100px;
  align-self: flex-start;
  position: relative;
}
.login_h6 {
  width: 100%;
  height: 90%;
  display: flex;
  justify-content: flex-end;
  flex-direction: column;
}
.login_h7 {
  height: 50px;
}
.login_h71 {
  height: 20px;
}
.login_h8 {
  font-size: 28px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: rgba(255, 255, 255, 1);
  line-height: 40px;
  position: absolute;
  left: 170px;
  top: 14px;
  cursor: hand;
}
input {
  outline: none;
  border: none;
  border-bottom: 1px solid #d8d8d8;
  padding: 7px 0;
  color: #999999;
  font-size: 14px;
  font-weight: 400;
  width: 80%;
}
.h8 {
  font-size: 18px;
  font-family: PingFangSC-Regular, PingFangSC;
  font-weight: 400;
  color: rgba(153, 153, 153, 1);
  position: relative;
  left: 100px;
  top: 80px;
}
</style>