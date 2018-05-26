<template>
  	<div>
        <head-top signin-up='home'>
            <span slot='logo' class="head_logo"  @click="reload">ele.me</span>
        </head-top>
        <nav class="city_nav">
            <div class="city_tip">
                <span>当前定位城市:</span>
                <span>定位不准时,请在城市列表中选择</span>
            </div>
            <router-link :to="'/city/'+guessCityid" class="guess_city">
                <span>{{guessCity}}</span>
                <svg class="arrow_right">
                    <use xmlns:xlink="http://www.w3c.org/1999/xlink" xlink:href="#arrow-right"></use>
                </svg>
            </router-link>
        </nav>
        <section id="hot_city_container">
            <h4 class="city_title">热门城市</h4>
            <ul class="citylistul clears">
                <router-link tag="li" v-for="item in hotcity" :to="'/city/'+item.id" :key="item.id">
                    {{item.name}}
                </router-link>
            </ul>
        </section>
        <section class="group_city_container">
            <ul class="letter_classify">
            	<!--key 的特殊属性主要用在 Vue的虚拟DOM算法，在新旧nodes对比时辨识VNodes。如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用key，它会基于key的变化重新排列元素顺序，并且会移除key不存在的元素。
				有相同父元素的子元素必须有独特的key。重复的key会造成渲染错误。-->
                <li v-for="(value,key,index) in sortgroupcity" :key="key" class="letter_classify_li">
                    <h4 class="city_title">
                        {{key}}
                        <span v-if="index==0">(按字母排序)</span>
                    </h4>
                    <ul class="groupcity_name_container citylistul clear">
                        <router-link tag="li" v-for="item in value" :to="'/city/'+item.id" :key="item.id" class="elipais">
                            {{item.name}}
                        </router-link>
                    </ul>
                </li>
            </ul>
        </section>
        <foot-guide></foot-guide>
    </div>
</template>


<script>
import headTop from '../../components/header/head'
import footGuide from '../../components/footer/footGuide.vue'
import {cityGuess, hotcity, groupcity} from '../../service/getData'


export default {
    data(){
        return{
            guessCity: '',   //当前城市
            guessCityid: '', //当前城市id
            hotcity: [],     //热门城市列表
            groupcity: {},   //所有城市列表
        }
    },


	mounted(){
        // 当前城市
        cityGuess().then(res=>{
            // console.log(res)
//          总结：
//			1.箭头函数的this绑定看的是this所在的函数定义在哪个对象下，绑定到哪个对象则this就指向哪个对象
//			2.如果有对象嵌套的情况，则this绑定到最近的一层对象上
            this.guessCity=res.name;
            this.guessCityid=res.id
        })
        // 热门城市
        hotcity().then(res=>{
            // console.log(res)
            this.hotcity=res;
        })
        //所有城市
        groupcity().then(res=>{
            console.log(res)
            this.groupcity=res;
        })
    },


    components:{
        headTop,
        footGuide
    },


    computed:{
        //将获取的数据按照A-Z字母开头排序
        sortgroupcity(){
            let sortobj = {};
            //遍历数字  
            for (let i = 65; i <= 90; i++) {
                if (this.groupcity[String.fromCharCode(i)]) {
                	
                    sortobj[String.fromCharCode(i)] = this.groupcity[String.fromCharCode(i)];
                }
            }
            console.log(sortobj)
            return sortobj
        }
    },


    methods:{
        //点击图标刷新页面
        reload(){
            window.location.reload();
        }
    },
}


</script>


<style lang="scss" scoped>
    @import '../../style/mixin';
    .head_logo{
        left: 0.4rem;
        font-weight: 400;
        @include sc(0.7rem, #fff);
        @include wh(2.3rem, 0.7rem);
        @include ct;
    }
    body{
        margin:0;
        padding: 0;
    }
    .city_nav{
        padding-top: 2.35rem;
        border-top: 1px solid $bc;
        background-color: #fff;
        margin-bottom: 0.4rem;
        .city_tip{
            @include fj;
            line-height: 1.45rem;
            padding: 0 0.45rem;
            span:nth-of-type(1){
                @include sc(0.55rem, #666);
            }
            span:nth-of-type(2){
                font-weight: 900;
                @include sc(0.475rem, #9f9f9f);
            }


        }
        .guess_city{
            @include fj;
            align-items: center;
            height: 1.8rem;
            padding: 0 0.45rem;
            border-top: 1px solid $bc;
            border-bottom: 2px solid $bc;
            @include font(0.75rem, 1.8rem);
            span:nth-of-type(1){
                color: $blue;
            }
            .arrow_right{
                @include wh(.6rem, .6rem);
                fill: #999;
            }
        }
    }
    #hot_city_container{
        background-color: #fff;
        margin-bottom: 0.4rem;
    }
    .citylistul{
        li{
            float: left;
            text-align: center;
            color: $blue;
            border-bottom: 0.025rem solid $bc;
            border-right: 0.025rem solid $bc;
            @include wh(25%, 1.75rem);
            @include font(0.6rem, 1.75rem);
            overflow: hidden;
        }
        li:nth-of-type(4n){
            border-right: none;
        }
    }
    .city_title{
        color: #666;
        font-weight: 400;
        text-indent: 0.45rem;
        border-top: 2px solid $bc;
        border-bottom: 1px solid $bc;
        @include font(0.55rem, 1.45rem, "Helvetica Neue");
        span{
            @include sc(0.475rem, #999);
        }
    }

    .letter_classify_li{
        margin-bottom: 0.4rem;
        background-color: #fff;
        border-bottom: 1px solid $bc;
        .groupcity_name_container{
            li{
                color: #666;
            }
        }
    }

</style>
