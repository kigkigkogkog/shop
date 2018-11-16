<template>
  <div>
    <nav-header></nav-header>
    <nav-bread>
        <span>商品</span>
    </nav-bread>
    <div class="accessory-result-page accessory-page">
    <div class="container">
        <div class="filter-nav">
        <span class="sortby">排序:</span>
        <a href="javascript:void(0)" class="price cur" v-on:click="sortGoods">价格<i class="iconfont" v-if="sortFlag==1">&#xe725;</i><i class="iconfont" v-else>&#xe724;</i></a>
        <a href="javascript:void(0)" class="filterby stopPop" v-on:click="showFilter">筛选条件</a>
        </div>
        <div class="accessory-result">
        <!-- filter -->
        <div class="filter stopPop" id="filter" v-bind:class="{'filterby-show':filterBy}">
            <dl class="filter-price">
                <dt>价格:</dt>
                <dd><a href="javascript:void(0)" v-on:click="setPriceFilter('all')" v-bind:class="{'cur':priceChecked=='all'}">All</a></dd>
                <dd v-for="(item,index) in priceFilter" :key="index">
                    <a href="javascript:void(0)"  v-on:click="setPriceFilter(index)" v-bind:class="{'cur':priceChecked==index}">{{item.startPrice}} - {{item.endPrice}}</a>
                </dd>
            </dl>
        </div>

        <!-- search result accessories list -->
        <div class="accessory-list-wrap">
            <div class="accessory-list col-4">
            <ul>
                <li v-for="(item,index) in goodsList" :key="index">
                    <div class="pic">
                        <a href="#"><img v-bind:src="'/static/'+item.productImg" alt=""></a>
                    </div>
                    <div class="main">
                        <div class="name">{{item.productName}}</div>
                        <div class="price">{{item.productPrice}}</div>
                        <div class="btn-area">
                        <a href="javascript:;" class="btn btn--m" v-on:click="addCart(item.productId)">加入购物车</a>
                        </div>
                    </div>
                </li>
            </ul>
            <div class="load-more" v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-distance="30">
                <img src="../../static/loading-svg/loading-spinning-bubbles.svg" v-show="loading">
            </div>
            </div>
        </div>
        </div>
    </div>
    </div>
    <div class="md-overlay" v-show="overLayFlag" v-on:blick="closePop"></div>
    <modal v-bind:mdShow="mdShow" v-bind:close="closeModal">
        <p slot="message">请先登录</p>
        <div slot="btnGroup">
            <a class="btn btn--m" v-on:click="mdShow=false">关闭</a>
        </div>
    </modal>
    <modal v-bind:mdShow="mdShowCart" v-bind:closeCart="closeCartModal">
        <p slot="message"><i class="iconfont">&#xe622;</i>加入购物车成功</p>
        <div slot="btnGroup">
            <a class="btn btn--m" href="javascript:void(0)" v-on:click="mdShowCart=false">继续购物</a>
            <router-link href="javascript:void(0)" to="/cart" class="btn btn--m">查看购物车</router-link>
        </div>
    </modal>
    <nav-footer></nav-footer>
  </div>
</template>

<script>
    import '../assets/css/base.css';
    import '../assets/css/product.css';
    import NavHeader from '../components/Header.vue';
    import NavFooter from '../components/Footer.vue';
    import NavBread from '../components/Bread.vue';
    import Modal from '../components/Modal.vue';
    import axios from 'axios';

    export default {
        data () {
            return {
                goodsList:[],
                priceFilter:[
                    {
                        startPrice:"0.00",
                        endPrice:"500.00"
                    },
                    {
                        startPrice:"500.00",
                        endPrice:"1500.00"
                    },
                    {
                        startPrice:"1500.00",
                        endPrice:"3000.00"
                    },
                    {
                        startPrice:"3000.00",
                        endPrice:"5000.00"
                    }
                ],
                priceChecked:"all",
                filterBy:false,
                overLayFlag:false,
                sortFlag:1,
                page:1,
                pageSize:6,
                busy:true,
                loading:false,
                mdShow:false,
                mdShowCart:false
            }
        },
        components:{
            NavHeader,
            NavFooter,
            NavBread,
            Modal
        },
        mounted(){
            this.getGoodsList();
        },
        methods:{
            getGoodsList(flag){
                let params = {
                    page:this.page,
                    pageSize:this.pageSize,
                    sort:this.sortFlag,
                    priceLevel:this.priceChecked
                }
                this.loading = true;
                axios.get("/goods/list",{params}).then(result=>{
                    this.loading = false;
                    var res = result.data;
                    if(res.status == 0){
                        if(flag){
                            if(res.result.count == 0){
                                this.busy = true;
                            }else{
                                this.busy = false;                                
                            }
                            this.goodsList = this.goodsList.concat(res.result.list);
                        }else{
                            this.goodsList = res.result.list;
                            this.busy = false;                                      
                        }
                    }else{
                        this.goodsList = [];
                        this.busy = false;                                
                    }
                })
            },
            setPriceFilter(index){
                this.priceChecked = index;
                this.page=1;
                this.getGoodsList(false);
                this.closePop();
            },
            showFilter(){
                this.filterBy=true;
                this.overLayFlag=true;
            },
            closePop(){
                this.filterBy=false;
                this.overLayFlag=false;
            },
            sortGoods(){
                this.sortFlag = - this.sortFlag;
                this.page = 1;
                this.getGoodsList();
            },
            loadMore(){
                this.busy = true;
                
                setTimeout(() => {
                    this.page++;
                    this.getGoodsList(true);
                }, 600);
            },
            addCart(productId){
                axios.post("/goods/addCart",{productId:productId}).then((res)=>{
                    if(res.data.status == 0){
                        this.mdShowCart = true;
                    }else{
                        this.mdShow = true;
                    }
                })
            },
            closeModal(){
                this.mdShow = false;
            },
            closeCartModal(){
                this.mdShowCart = false;
            }

        }
    }
</script>