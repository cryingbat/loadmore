<template>
    <div ref="wrapper" class="list-wrapper">
        <div class="scroll-content">
            <slot></slot>
            <div>
                <Loading v-show="inPullUp" :loadingWord='PullingUpWord'></Loading>
            </div>
            <transition name="pullDown">
	           <Loading class="pullDown" v-show="inPullDown" :loadingWord='PullingDownWord'></Loading>
	        </transition>
        </div>
    </div>
</template>

<script >
  import BScroll from 'better-scroll'
  import Loading from '@/base/loading.vue'

  const  PullingUpWord="正在拼命加载中...";
  const  beforePullUpWord="上拉加载更多";
  const  finishPullUpWord="加载完成";

  const  PullingDownWord="刷新中...";

  export default {
    props: {
      dataList:{
        type: Array,
        default: []
      },
      probeType: {
        type: Number,
        default: 3
      },
      click: {
        type: Boolean,
        default: true
      },
      pullDownRefresh: {
        type: null,
        default: false
      },
      pullUpLoad: {
        type: null,
        default: false
      },
      height: {
        type: Number,
        default: 200
      }
    },
    data() {
        return {
            scroll:null,
            inPullUp:false,
            inPullDown:false,
            beforePullUpWord,
            PullingUpWord,
            PullingDownWord,
            continuePullUp:true
        }
    },
    mounted() {
        let w = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
        let h = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight;
        this.$refs.wrapper.style.height = h + 'px';
        setTimeout(()=>{
            this.initScroll();

            this.scroll.on('pullingUp',()=> {
                if(this.continuePullUp){
                    this.beforePullUp();
                    this.$emit("onPullUp");
                }
            });

            this.scroll.on('pullingDown',()=> {
                this.beforePullDown();
                this.$emit("onPullDown");
            });

        },20)
    },
    methods: {
        initScroll() {
            if (!this.$refs.wrapper) {
                return
            }
            this.scroll = new BScroll(this.$refs.wrapper, {
                probeType: this.probeType,
                click: this.click,
                pullDownRefresh: this.pullDownRefresh,
                pullUpLoad: this.pullUpLoad,
            })
        },
        beforePullUp(){
            this.PullingUpWord=PullingUpWord;
            this.disable();
            this.inPullUp=true;
        },
        beforePullDown(){
            this.disable();
            this.inPullDown=true;
        },
        finish(type){
            this["finish"+type]();
            this.enable();
            this["in"+type]=false;
        },
        disable() {
            this.scroll && this.scroll.disable()
        },
        enable() {
            this.scroll && this.scroll.enable()
        },
        refresh() {
            this.scroll && this.scroll.refresh()
        },
        finishPullDown(){
            this.scroll&&this.scroll.finishPullDown()
        },
        finishPullUp(){
            this.scroll&&this.scroll.finishPullUp()
        },
    },
    watch: {
        dataList() {
            this.$nextTick(()=>{
                this.refresh();
            })
        }
    },
    components: {
        Loading
    }
  }

</script>

<style lang="less" scoped>
   .list-wrapper{
    height: 300px;
    background: #fff;
    overflow: hidden;
    .scroll-content{
        padding-bottom: 30px;
    }
  }
    .list-content{
      position: relative;
      z-index: 10;
      background: #fff ;
    }
      .list-item{
        height: 60px;
        line-height: 60px;
        font-size: 18px;
        padding-left: 20px;
        border-bottom: 1px solid #e5e5e5;
      }
  .pulldown-wrapper{
    position: absolute;
    width: 100%;
    left: 0;
    display: flex;
    justify-content :center;
    align-items: center;
    transition: all;
  }
  .pullup-wrapper{
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 16px 0;
  }


.pullDown{
    position: absolute;
    top:0;
    left:0;
}
    .pullDown-enter-active{
        transition:all 0.2s;
    }
    .pullDown-enter, .pullDown-leave-active{
        transform:translateY(-100%);
        transition:all 0.2s;
    }
</style>
