<template>
<div>
    <nav-header></nav-header>
    <nav-bread>
        <span>订单列表</span>
    </nav-bread>
    <div>
        <div class="container">
            <div class="checkout-order">
                <div class="page-title-normal">
                    <h2 class="page-title-h2"><span>确认</span></h2>
                </div>
                <!-- process step -->
                <div class="check-step">
                    <ul>
                        <li class="cur">确认地址</li>
                        <li class="cur">查看订单</li>
                        <li>支付</li>
                        <li>订单确认</li>
                    </ul>
                </div>

                <!-- order list -->
                <div class="page-title-normal checkout-title">
                    <h2><span>订单详情</span></h2>
                </div>
                <div class="item-list-wrap confirm-item-list-wrap">
                    <div class="cart-item order-item">
                    <div class="cart-item-head">
                        <ul>
                        <li>订单内容</li>
                        <li>价格</li>
                        <li>数量</li>
                        <li>小计</li>
                        </ul>
                    </div>
                    <ul class="cart-item-list">
                        <li v-for="(item,index) in cartList" :key="index" v-if="item.checked=='1'">
                        <div class="cart-tab-1">
                            <div class="cart-item-pic">
                            <img v-bind:src="'/static/'+item.productImg" alt="">
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
                            <div class="select-self">
                                <div class="select-self-area">
                                <span class="select-ipt">×{{item.productNum}}</span>
                                </div>
                            </div>
                            </div>
                        </div>
                        <div class="cart-tab-4">
                            <div class="item-price-total">￥{{item.productNum*item.productPrice}}</div>
                        </div>
                        </li>
                    </ul>
                    </div>
                </div>

                <!-- Price count -->
                <div class="price-count-wrap">
                    <div class="price-count">
                    <ul>
                        <li>
                        <span>总价:</span>
                        <span>￥{{subTotal}}</span>
                        </li>
                        <li>
                        <span>配送费:</span>
                        <span>￥{{shipping}}</span>
                        </li>
                        <li>
                        <span>折扣:</span>
                        <span>￥{{discount}}</span>
                        </li>
                        <li class="order-total-price">
                        <span>订单总价:</span>
                        <span>￥{{orderTotal}}</span>
                        </li>
                    </ul>
                    </div>
                </div>

                <div class="order-foot-wrap">
                    <div class="prev-btn-wrap">
                    <router-link class="btn btn--m" to="/address">上一步</router-link>
                    </div>
                    <div class="next-btn-wrap">
                    <button class="btn btn--m btn--red" @click="payMent">付款</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
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
            cartList:[],
            shipping:100,
            discount:50,
            subTotal:0,
            orderTotal:0
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
    methods:{
        init(){
            axios.get('/users/cartList').then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    this.cartList = res.result;
                    this.cartList.forEach((item)=>{
                        if(item.checked=='1'){
                            this.subTotal += item.productNum*item.productPrice;
                        }
                    })
                    this.orderTotal = this.subTotal + this.shipping - this.discount;
                }
            })
        },
        payMent(){
            var addressId = this.$route.query.addressId;
            axios.post('/users/payMent',{
                    orderTotal:this.orderTotal,
                    addressId:addressId
                }).then((data)=>{
                    var res = data.data;
                    if(res.status == 0){
                        this.$router.push({
                            path:'/orderSuccess?orderId='+res.result.orderId
                        })
                    }
            })
        }
    }
}

</script>
<style scoped>
</style>
