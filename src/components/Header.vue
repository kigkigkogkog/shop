<template>
 <header class="header">
    <div class="navbar">
        <div class="navbar-right-container" style="float:right;margin:24px 20px 0 0">
            <div class="navbar-menu-container">
                <span class="navbar-link" v-text="nickName" v-show="nickName"></span>
                <a href="javascript:void(0)" class="navbar-link" style="margin-right:5px" v-if="nickName" @click="logout">退出</a>
                <a href="javascript:void(0)" class="navbar-link" style="margin-right:5px" v-else @click="loginModalFlag=true">登录</a>
                <div class="navbar-cart-container" style="display:inline-block">
                    <span class="navbar-cart-count"></span>
                    <a class="navbar-link navbar-cart-link" href="/#/cart">
                        <i class="iconfont" style="font-size:26px;">&#xe608;</i>
                    </a>
                </div>
            </div>
        </div>
        <div class="navbar-left-container">
            <a href="/">
                <img class="navbar-brand-logo" src="../../static/mi.jpg"  style="margin:5px 0 5px 5px">
            </a>
        </div>
    </div>
    <div class="md-modal modal-msg md-modal-transition" v-bind:class="{'md-show':loginModalFlag}">
        <div class="md-modal-inner">
        <div class="md-top">
            <div class="md-title">登录</div>
            <button class="md-close" @click="loginModalFlag=false">关闭</button>
        </div>
        <div class="md-content">
            <div class="confirm-tips">
            <div class="error-wrap">
                <span class="error error-show" v-show="errorTip">用户名或者密码错误</span>
            </div>
            <ul>
                <li class="regi_form_input">
                <i class="icon IconPeople"></i>
                <input type="text" tabindex="1" name="loginname" v-model="userName" class="regi_login_input regi_login_input_left" placeholder="账号" data-type="loginname">
                </li>
                <li class="regi_form_input noMargin">
                <i class="icon IconPwd"></i>
                <input type="password" tabindex="2"  name="password" v-model="userPwd" class="regi_login_input regi_login_input_left login-input-no input_text" placeholder="密码" @keyup.enter="login">
                </li>
            </ul>
            </div>
            <div class="login-wrap">
            <a href="javascript:;" class="btn-login" @click="login">登  录</a>
            </div>
        </div>
        </div>
    </div>
    <div class="md-overlay" v-if="loginModalFlag" @click="loginModalFlag=false"></div>
    </header>
</template>

<script>
import '../assets/css/login.css';
import axios from 'axios';
export default {
  data() {
    return {
        nickName:"",
        userName:'',
        userPwd:'',
        errorTip:false,
        loginModalFlag:false
    };
  },
  mounted(){
      this.checkLogin();
  },
  methods:{
      login(){
          axios.post('/users/login',{userName:this.userName,userPwd:this.userPwd}).then((result)=>{
              var res = result.data;
              if(res.status == 0){
                this.errorTip=false;
                this.loginModalFlag=false;
                this.nickName = res.result.userName;
              }else{
                this.errorTip=true;
              }
          })
      },
      logout(){
          axios.post('/users/logout').then((result)=>{
              var res = result.data;
              if(res.status == 0){
                this.nickName = "";
              }
          })
      },
      checkLogin(){
          axios.get('/users/checkLogin').then((result)=>{
              var res = result.data;
              if(res.status == 0){
                this.nickName = res.result.userName;
              }
          })
      }
  }
};
</script>
