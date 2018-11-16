<template>
<div>
    <nav-header></nav-header>
    <div class="container">
        <div class="page-title-normal">
        <h2 class="page-title-h2"><span>check out</span></h2>
        </div>
        <!-- 进度条 -->
        <div class="check-step">
        <ul>
            <li class="cur">确认地址</li>
            <li class="cur">查看订单</li>
            <li class="cur">支付</li>
            <li class="cur">订单确认</li>
        </ul>
        </div>

        <div class="order-create">
        <div class="order-create-pic"><img src="../../static/ok-2.png" alt=""></div>
        <div class="order-create-main">
            <h3>祝贺! <br>我们会尽快发货!</h3>
            <p>
            <span>订单ID：{{orderId}}</span>
            <span>订单金额：{{orderTotal}}</span>
            </p>
            <div class="order-create-btn-wrap">
            <div class="btn-l-wrap">
                <router-link href="javascript:;" class="btn btn--m" to="/cart">购物车列表</router-link>
            </div>
            <div class="btn-r-wrap">
                <router-link href="javascript:;" class="btn btn--m" to="/">商品列表</router-link>
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
                orderId:'',
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
            var orderId = this.$route.query.orderId;
            if(!orderId){
                return;
            }
            axios.get('/users/orderDetails',{params:{orderId:orderId}}).then((data)=>{
                var res = data.data;
                if(res.status == 0){
                    this.orderId = res.result.orderId;
                    this.orderTotal = res.result.orderTotal;
                }
            })
        }
    }

</script>
<style scoped>
</style>
