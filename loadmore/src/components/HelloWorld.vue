<template>
  <section>
  <Loading id="saerchingLoading" v-show="inSearching"></Loading>
   <Scroller
        id="scroll"
        ref="scroll"
        :height="500"
        :dataList="filmList"
        :pullDownRefresh="DOWN_CONFIG"
        :pullUpLoad="UP_CONFIG"
        @onPullUp="pullUpHandle"
        @onPullDown="pullDownHandle"
      >
        <ul class="item">
             <router-link class="film-list" v-for="(v,i) in filmList" :key="v.id" tag="li" :to='{path:"/"}'>
                    <div class="film-list__img" >
                         <img :src="v.picUrl" width='100' alt="" />
                    </div>
                    <div class="film-list__detail">
                        <p class="film-list__detail__title">{{v.title}}</p>
                        <p class="film-list__detail__director">描述：{{v.description}}</p>
                    </div>
                </router-link>
          </ul>
      </Scroller>
  </section>
</template>
<script>
import axios from 'axios'
import Scroller from '@/base/scroll'
import Loading from '@/base/loading'
var UP_CONFIG={
  threshold:-80,
}
var DOWN_CONFIG={
  threshold:80,
  stop:40
}
export default {
    components: {
      Scroller,
      Loading
    },
    data () {
        return {
          filmList:[],
          inSearching:false,
          page:0,
          DOWN_CONFIG,
          UP_CONFIG
        };
    },
    computed:{
        scrollElement(){
            return this.$refs.scroll
        }
    },
    mounted() {
      this.fetchData(); //第一次渲染数据
    },
    methods: {
      pullUpHandle(){
        this.loadmore();//下拉加载更多
      },
      pullDownHandle(){
        setTimeout(()=>{//上拉刷新数据
          this.scrollElement.finish("PullDown");
          //这里处理上拉刷新完之后的逻辑
          var l=this.filmList.length;
             var random=Math.floor(Math.random()*l);
          
          // console.log(this.filmList[random]);
             this.filmList.unshift(this.filmList[random]);
        },2000)
      },
       filterDirectors(arr){
          var name="";
          arr.forEach((item,i)=>{
              if(i==arr.length-1){
                 name+=item.name
              }else{
                 name+=item.name+" / "
              }
          })
          return name
      },
      loadmore() {
        axios.get('/url', {
            params: {
              //参数
              
            }
          })
          .then((res)=>{
            //数据处理
              res=res.data;
              if(res.length>0){
                 this.scrollElement.PullingUpWord="加载完成";
                 setTimeout(()=>{
                     this.scrollElement.finish("PullUp");
                     this.filmList=this.filmList.concat(res);
                 },1000)
              }else{
                  alert("没有更多数据了！");
              }
              this.inSearching=false;
          })
          
      },
      fetchData(){
          axios.get('/url', {
            params: {
              //参数
              
            }
          })
          .then((res)=>{
            res = res.data;
              
              if(res.length>0){
                  this.filmList = res;
              }else{
                  alert("暂无数据");
              }
              this.inSearching=false;
         })
      }
    }
  }
</script>
<style lang='less' scoped>
  #scroll{
        top:1.11rem;
        bottom:1.11rem;
        .item{
          list-style:none;
        }
    }
</style>