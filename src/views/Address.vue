<template>
<div>
    <nav-header></nav-header>
    <nav-bread>
        <span>地址列表</span>
    </nav-bread>
 <div class="checkout-page">
  <div class="container">
    <div class="checkout-addr">
      <div class="page-title-normal">
        <h2 class="page-title-h2"><span>check out</span></h2>
      </div>
      <!-- process step -->
      <div class="check-step">
        <ul>
          <li class="cur">确认地址</li>
          <li>查看订单</li>
          <li>支付</li>
          <li>订单确认</li>
        </ul>
      </div>

      <!-- address list -->
      <div class="page-title-normal checkout-title">
        <h2><span>Shipping address</span></h2>
      </div>
      <div class="addr-list-wrap">
        <div class="addr-list">
          <ul>
            <li v-for="(item,index) in addressListFilter" :key="index" v-bind:class="{'check':checkIndex==index}" @click="checkIndex=index;orderAddressId=item.addressId;">
              <dl>
                <dt>{{item.userName}}</dt>
                <dd class="address">{{item.streetName}}</dd>
                <dd class="tel">{{item.tel}}</dd>
              </dl>
              <div class="addr-opration addr-del">
                <a href="javascript:;" class="addr-del-btn" @click="deleteShow(item.addressId)">
                    <i class="iconfont" style="font-size:22px;">&#xe61e;</i>
                </a>
              </div>
              <div class="addr-opration addr-set-default">
                <a href="javascript:;" class="addr-set-default-btn" v-if="!item.isDefault" @click="setDefault(item.addressId)"><i>设为默认</i></a>
              </div>
              <div class="addr-opration addr-default" v-if="item.isDefault">默认地址</div>
            </li>
            <li class="addr-new">
              <div class="add-new-inner">
                <i class="iconfont" style="font-size:60px;" @click="loginModalFlag=true">&#xe617;</i>
                <p>添加新地址</p>
              </div>
            </li>
          </ul>
        </div>

        <div class="shipping-addr-more">
          <a class="addr-more-btn up-down-btn" href="javascript:;" @click="expand" v-bind:class="{'open':limit>3}">
            <span v-if="limit<4">更多</span>
            <span v-else>收起</span>
            <i class="i-up-down">
              <i class="i-up-down-l"></i>
              <i class="i-up-down-r"></i>
            </i>
          </a>
        </div>
      </div>

      <!-- shipping method-->
      <div class="page-title-normal checkout-title">
        <h2><span>Shipping method</span></h2>
      </div>
      <div class="lemall-msg-info hidden">
        <span>The region you selected is not within our delivery area. Please select another shipping address within our delivery areas.</span>
      </div>
      <div class="shipping-method-wrap">
        <div class="shipping-method">
          <ul>
            <li class="check">
              <div class="name">Standard shipping</div>
              <div class="price">Free</div>
              <div class="shipping-tips">
                <p>Once shipped，Order should arrive in the destination in 1-7 business days</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="next-btn-wrap">
        <router-link class="btn btn--m btn--red" v-bind:to="{path:'orderConfirm',query:{'addressId':orderAddressId}}">下一步</router-link>
      </div>
    </div>
  </div>
</div>
<div class="md-modal modal-msg md-modal-transition" v-bind:class="{'md-show':loginModalFlag}">
    <div class="md-modal-inner">
    <div class="md-top">
        <div class="md-title">新增地址</div>
        <button class="md-close" @click="loginModalFlag=false">关闭</button>
    </div>
    <div class="md-content">
        <div class="confirm-tips">
        <ul>
            <li class="regi_form_input">
            <input type="text" tabindex="1" name="userName" v-model="userName" class="regi_login_input regi_login_input_left" placeholder="昵称">
            </li>
            <li class="regi_form_input noMargin">
            <input type="text" tabindex="2"  name="streetName" v-model="streetName" class="regi_login_input regi_login_input_left" placeholder="地址">
            </li>
            <li class="regi_form_input noMargin">
            <input type="text" tabindex="3"  name="postCode" v-model="postCode" class="regi_login_input regi_login_input_left" placeholder="邮编">
            </li>
            <li class="regi_form_input noMargin">
            <input type="text" tabindex="4"  name="tel" v-model="tel" class="regi_login_input regi_login_input_left" placeholder="手机">
            </li>
        </ul>
        </div>
        <div class="login-wrap">
        <a href="javascript:;" class="btn-login" @click="addAddress">确定</a>
        </div>
    </div>
    </div>
</div>
<div class="md-overlay" v-if="loginModalFlag" @click="loginModalFlag=false"></div>

<modal v-bind:mdShow="modalSure" v-bind:close="closeModal">
    <p slot="message">
        是否确认删除地址？
    </p>
    <div slot="btnGroup">
        <a class="btn btn--m" href="javascript:void(0)" v-on:click="deleteAddress">确定</a>
        <a class="btn btn--m" href="javascript:void(0)" v-on:click="modalSure=false">取消</a>
    </div>
</modal>
<modal v-bind:mdShow="modalSure" v-bind:close="closeModal">
    <div slot="btnGroup">
        <a class="btn btn--m" href="javascript:void(0)" v-on:click="deleteAddress">确定</a>
        <a class="btn btn--m" href="javascript:void(0)" v-on:click="modalSure=false">取消</a>
    </div>
</modal>
<nav-footer></nav-footer> 
</div>
</template>

<script>
    import '../assets/css/base.css';
    import '../assets/css/checkout.css';
    import NavHeader from '../components/Header.vue';
    import NavFooter from '../components/Footer.vue';
    import NavBread from '../components/Bread.vue';
    import Modal from '../components/Modal.vue';
    import axios from 'axios';
export default {
    data () {
        return {
            limit:3,
            addressList:[],
            checkIndex:0,
            modalSure:false,
            addressId:"",
            orderAddressId:"",
            loginModalFlag:false,
            userName:'',
            streetName:'',
            tel:'',
            postCode:''
        }
    },
    components:{
        NavHeader,
        NavFooter,
        NavBread,
        Modal
    },
    mounted(){
        this.init();
    },
    computed:{
        addressListFilter(){
            return this.addressList.slice(0,this.limit);
        }
    },
    methods:{
        init(){
            axios.get("/users/addressList").then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    this.addressList = res.result;
                }              
            })
        },
        expand(){
            if(this.limit == 3){
                this.limit = this.addressList.length;
            }else{
                this.limit = 3;
            }
        },
        setDefault(addressId){
            axios.post("/users/setDefault",{addressId:addressId}).then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    console.log('成功');
                    this.init();
                }              
            })
        },
        closeModal(){
            this.modalSure = false;
        },
        deleteShow(addressId){
            this.modalSure = true;
            this.addressId = addressId;
        },
        deleteAddress(){
            axios.post("/users/delAddress",{
                addressId:this.addressId
            }).then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    this.modalSure = false;
                    this.init();
                }              
            })
        },
        addAddress(){
            axios.post("/users/addAddress",{
                userName:this.userName,
                streetName:this.streetName,
                postCode:this.postCode,
                tel:this.tel
            }).then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    this.init();
                    console.log("true");
                }              
            })
        }
    }
}

</script>
<style scoped>
</style>
