<template>
	<div class="loading"><span>{{loadingWord}}</span></div>
</template>

<script>
export default {
    name: 'loading',
    props:{
    	loadingWord:{
    		type:String
    	}
    }
};
</script>

<style lang="less" scoped>
	.loading{
		text-align: center;
		z-index: 100;
		width: 100%;
		height:1rem;
		line-height: 1rem;				
		img{
			margin: 0 auto;
			width: 0.65rem;
			height: 0.65rem;
			vertical-align: middle;
		}
		span{
			vertical-align: middle;
			margin-left: 0.14rem;
			font-size: 14px;
		}
	}
</style>