<!-- 比价搜索列表页中心组件 -->
<template>
    <div>
        <!--商品列表-->
        <section id="productList">
            <!-- 面包屑组件 -->
            <BreadCrumb></BreadCrumb>
            <!-- 主体商品区域 -->
            <div class="product-list">
                <div class="media">
                    <router-link v-for="(value,index) in listData" :key="index" :to="'/compare?categoryname='+$route.query.categoryname+'&productId='+value.productId">
                        <div class="pic" v-html="value.productImg"></div>
                        <div class="media-body">
                            <div class="tit">{{value.productName}}</div>
                            <div class="price"><em>{{value.productPrice}}</em></div>
                            <div class="other"><em>{{value.productQuote}}</em><em>{{value.productCom}}</em></div>
                        </div>
                    </router-link>
                </div>
            </div>
            <!-- 商品的操作  上一页下一页-->
            <div class="clearfix page">
                <span class="w33">
                    <a @click="pagePrev()" data-index="down">上一页</a>
                </span>
                <span class="w33">
                    <select id="selectPage" v-model="nowPage" @change="pageChange()" name="select">
                        <option :value="n" v-for="n in totalPage" :selected="n==nowPage">{{n}}/{{totalPage}}</option>
                    </select>
                </span>
                <span class="w33">
                    <a @click="pageNext()" data-index="up">下一页</a>
                </span>
            </div>
        </section>
    </div>
</template>
<script>
// 导入面包屑组件
import BreadCrumb from './BreadCrumb.vue'
// 发送请求标志位
let sendFlag = false;
export default {
    name: 'ProductList',
    data() {
        return {
            // 商品列表
            listData: [],
            // 发送请求（数据）中的页数
            pageid:1,
            // 总页数
            totalPage:1,
            // 当前显示的页数
            nowPage:1,
            // 获取分类
            categoryid:0
        }
    },
    mounted() {
        // 获取分类
        this.categoryid = this.$route.query.categoryId;
        this.getData((resBody)=>{
            this.totalPage = Math.ceil(resBody.totalCount/resBody.pagesize);
        });
    },
    methods:{
        getData(callback){
            // 获取列表数据
            this.$http.jsonp('http://139.199.157.195:9090/api/getproductlist',{
                params:{
                    'categoryid': this.categoryid,
                    'pageid':this.pageid
                }
            }).then(function(res) {
                // 返回至顶部
                document.body.scrollTop = 0;
                // 更新页面数据
                this.listData = res.body.result;
                // 设置当前页为数据对应的页数
                this.nowPage = this.pageid;
                sendFlag = false;
                if (callback) {
                    callback(res.body);
                }
            });
        },
        pageNext(){
            // 如果已发送请求，则直接返回
            if (sendFlag) {return;}
            sendFlag = true;
            // 判断是否为最大页
            if (this.pageid>=this.totalPage) {return;}
            this.pageid++;
            this.getData();
        },
        pagePrev(){
            // 如果已发送请求，则直接返回
            if (sendFlag) {return;}
            sendFlag = true;
            // 判断是否为最小页
            if (this.pageid<=1) {return;}
            this.pageid--;
            this.getData();
        },
        pageChange(){
            // 如果已发送请求，则直接返回
            if (sendFlag) {return;}
            sendFlag = true;
            // 将要申请的页数设置为双向绑定的select选中的value
            this.pageid = this.nowPage;
            this.getData();
        }
    },
    // 注册组件
    components: {
        BreadCrumb
    }
}
</script>
<style scoped lang="less">
//定义水平居中方法
.textAlign() {
    text-align: center;
}

//图片居中方法
.verticalAlign() {
    vertical-align: middle;
}

//定义下边框
.borderBottom {
    border-bottom: 1px solid #bababa;
}

//商品列表
//主体商品区域
.product-list {
    > .media {
        > a {
            //给a标签设置样式
            //下边框
            border-bottom: 1px solid #ccc;
            text-decoration: none;
            //变为块级元素 才可以在里面放块级元素
            display: block;
            padding: 10px;
            > .pic {
                //给左边的图片设置左浮动
                float: left;
                display: inline-block;
                .verticalAlign();
            }
            > .media-body {
                //给右侧价格区域设置样式
                font-size: 14px;
                line-height: 24px;
                padding-left: 10px;
                > .tit {
                    //给标题设置下边距
                    margin-bottom: 35px;
                }
                > .price {
                    //给价格设置样式
                    > em {
                        font-size: 18px;
                        color: #ea3900;
                        .verticalAlign();
                    }
                }
                > .other {
                    //给评论设置样式
                    color: #a1a1a1;
                    font-size: 12px;
                    > em:first-child {
                        //em:first-child 父元素的第一个em元素
                        //给报价设置右边距
                        margin-right: 10px;
                    }
                }
            }
        }
    }
}

//上一页 下一页 区域 设置样式
.page {
    //给翻页区域设置 内容水平居中
    margin-top: 10px !important;
    .textAlign();
    > .w33 {
        //给翻页按钮设置样式 三等分 浮动 内容水平居中
        width: 33.33%;
        float: left;
        .textAlign();
        > a {
            //给上下页设置样式
            //压缩内容的宽高
            box-sizing: border-box;
            //背景颜色
            background-color: #d4d4d4;
            //下边框
            .borderBottom();
            //盒子阴影
            box-shadow: 0 0 1px #666;
            //圆角
            border-radius: 5px;
            .textAlign();
            font-size: 14px;
            line-height: 32px;
            overflow: hidden;
            padding: 8px 20px;
        }
        > #selectPage {
            //给下拉框设置样式
            //下边框
            //.borderBottom();
            font-size: 16px;
            padding: 8px 15px;
            height: 36px;
            &:focus {
                //去除下拉框边框的颜色
                border-color: blue;
            }
            >option {
                border-color: white;
                &:hover {
                    background-color: red;
                }
            }
        }
    }
}
</style>
