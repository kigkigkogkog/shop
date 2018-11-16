<template>
 <div>
    <nav-header></nav-header>
    <nav-bread>
        <span>购物车</span>
    </nav-bread>
    <div class="container">
        <div class="cart">
            <div class="page-title-normal">
                <h2 class="page-title-h2"><span>我的购物车</span></h2>
            </div>
            <div class="item-list-wrap">
                <div class="cart-item">
                    <div class="cart-item-head">
                        <ul>
                            <li>商品</li>
                            <li>价格</li>
                            <li>数量</li>
                            <li>小计</li>
                            <li>操作</li>
                        </ul>
                    </div>
                    <ul class="cart-item-list">
                        <!-- v-for="item in cartList" -->
                        <li v-for="(item,index) in cartList" :key="index">
                            <div class="cart-tab-1">
                            <div class="cart-item-check">
                                <a href="javascipt:;"   @click="editCart('checked',item)">
                                    <i class="iconfont" v-if="item.checked=='0'">&#xe614;</i>
                                    <i class="iconfont" v-else>&#xe613;</i>
                                </a>
                            </div>
                            <div class="cart-item-pic">
                                <img v-bind:src="'/static/'+item.productImg">
                            </div>
                            <div class="cart-item-title">
                                <div class="item-name">{{item.productName}}</div>
                            </div>
                            </div>
                            <div class="cart-tab-2">
                            <div class="item-price">{{item.productPrice}}</div>
                            </div>
                            <div class="cart-tab-3">
                            <div class="item-quantity">
                                <div class="select-self select-self-open">
                                <div class="select-self-area">
                                    <a class="input-sub" @click="editCart('sub',item)">-</a>
                                    <span class="select-ipt">{{item.productNum}}</span>
                                    <a class="input-add" @click="editCart('add',item)">+</a>
                                </div>
                                </div>
                            </div>
                            </div>
                            <div class="cart-tab-4">
                            <div class="item-price-total">{{item.productNum*item.productPrice}}</div>
                            </div>
                            <div class="cart-tab-5">
                            <div class="cart-item-opration">
                                <a href="javascript:;" class="item-edit-btn" v-on:click="deleteShow(item.productId)">
                                    <i class="iconfont" style="font-size:25px;">&#xe61e;</i>
                                </a>
                            </div>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
            <div class="cart-foot-wrap">
            <div class="cart-foot-inner">
                <div class="cart-foot-l">
                <div class="item-all-check">
                    <a href="javascipt:;" @click="toggleCheckAll">
                        <i class="iconfont" v-if="checkAllFlag">&#xe613;</i>
                        <i class="iconfont" v-else>&#xe614;</i>
                        
                    <span>全选</span>
                    </a>
                </div>
                </div>
                <div class="cart-foot-r">
                <div class="item-total">
                    合计: <span class="total-price">{{totalPrice}}</span>
                </div>
                <div class="btn-wrap">
                    <a class="btn btn--red" v-bind:class="{'btn--dis':checkedCount==0}" @click="checkOut">去结算</a>
                </div>
                </div>
            </div>
            </div>
        </div>
        </div>
    <modal v-bind:mdShow="modalSure" v-bind:close="closeModal">
        <p slot="message">你确定删除此条数据</p>
        <div slot="btnGroup">
            <a class="btn btn--m" href="javascript:void(0)" v-on:click="deleteCart">确定</a>
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
    data() {
        return {
            cartList:[],
            modalSure:false,
            productId:0
        };
    },
    mounted(){
        this.init();
    },
    computed:{
        checkAllFlag(){
            return this.checkedCount == this.cartList.length;
        },
        checkedCount(){
            var i = 0;
            this.cartList.forEach((item)=>{
                if(item.checked == "1"){
                    i++;
                }                
            })
            return i;
        },
        totalPrice(){
            var money = 0;
            this.cartList.forEach((item)=>{
                if(item.checked == "1"){
                    money += item.productPrice*item.productNum;
                }                
            })
            return money;
        }
    },
    components:{
        NavHeader,
        NavFooter,
        NavBread,
        Modal
    },
    methods:{
        init(){
            axios.get("/users/cartList").then((result)=>{
                var res = result.data;
                if(res.status == 0){
                    this.cartList = res.result;
                }              
            })
        },
        deleteShow(productId){
            this.productId = productId;
            this.modalSure = true;
        },
        deleteCart(){
            axios.post("/users/cart/del",{
                productId:this.productId
            }).then((result)=>{
                var res = result.data;
                console.log(res);
                if(res.status == 0){
                    this.modalSure = false;
                    this.init();
                }              
            })
        },
        closeModal(){
            this.modalSure = false;
        },
        editCart(flag,item){
            switch (flag){
                case "add":
                    item.productNum++;
                    break;
                case "sub":
                    if(item.productNum<=1){
                        break;
                    }else{
                        item.productNum--;
                        break;
                    }    
                case "checked":
                    item.checked=="1" ? item.checked="0" : item.checked="1";
                    break;
            }

            axios.post("/users/cart/edit",{
                productId:item.productId,
                productNum:item.productNum,
                checked:item.checked
            }).then((result)=>{
                var res = result.data;
                if(res.status == 0){
                }else{
                }              
            })
        },
        toggleCheckAll(){
            var flag = !this.checkAllFlag;
            this.cartList.forEach((item)=>{
                item.checked = flag?"1":"0";
            })
            axios.post("/users/cart/editAll",{
                checkAll:(flag?"1":"0")
            }).then((result)=>{
                var res = result.data;
                if(res.status == 0){
                    console.log("操作成功");
                }else{
                    console.log("操作失败");
                }              
            })
        },
        checkOut(){
            if(this.checkedCount>0){
                this.$router.push({path:'/address'});
            }
        }
    }
};
</script>
<style scoped>

.input-sub,.input-add{
      min-width: 40px;
      height: 100%;
      border: 0;
      color: #605F5F;
      text-align: center;
      font-size: 16px;
      overflow: hidden;
      display: inline-block;
      background: #f0f0f0;
    }
    .item-quantity .select-self-area{
      background:none;
      border: 1px solid #f0f0f0;
    }
    .item-quantity .select-self-area .select-ipt{
      display: inline-block;
      padding:0 3px;
      width: 30px;
      min-width: 30px;
      text-align: center;
    }
</style>
