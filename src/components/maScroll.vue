<template>
  <div id="wrapper">
  	<div class="scroller">
  		<slot></slot>
  		<div v-if="showMsg">
  				<div v-if="up && !pulldown" class="loadmore">下拉刷新~</div>
  				<div v-if="up && pulldown" class="loadmore">松开刷新~</div>
	      	<div v-if="down && !allLoaded" class="loadover">上拉查看更多~</div>
	      	<div v-if="down && allLoaded" class="loadover">我是有底线的~</div>
	    </div>
  	</div>
  </div>
</template>

<script>
	import IScroll from '../lib/iscroll-probe'
  /* eslint-disable */
  export default {
    name: 'ma-scroll',
    props: {
      getMore: {
        type: Function,
        default () {
          return false
        }
      },
      allLoaded: {
        type: Boolean,
        default () {
          return false
        }
      },
      up:{
      	type: Boolean,
        default () {
          return false
        }

      },
      down:{
      	type: Boolean,
        default () {
          return false
        }

      },
      refresh:{
      	type: Function,
        default () {
          return false
        }
      },
      showMsg: {
      	type: Boolean,
        default () {
          return false
        }
      },
      init:{
      	type: Boolean
      }
    },
    methods:{
    	
    },
     watch: {
     	allLoaded(newValue,oldValue){
	    	this.pullup = newValue
	   },
	   init(newValue,oldValue){
	   		return newValue
	   }
	  },
    data () {
      return {
        pullup:false,
        pulldown:false,
        once:true,
//      notFull:false,
//      hidePulldown:true
      }
    },
    methods: {//取可以滚动的元素
    	
    },
    mounted(){
    	let that = this
    	let myScroll = new IScroll('#wrapper', {
						mouseWheel: true,
						click:true,
						probeType: 3
					});
					
					//加入延时，保证dom已加载完成
//						setTimeout(function(){
//							if(document.getElementsByClassName('scroller')[0].scrollHeight < myScroll.scrollerHeight){
//									that.notFull = true
//								}else{
//									that.notFull = false
//								}
//								
//								if(document.getElementsByClassName('scroller')[0].scrollHeight >= 35){
//									that.hidePulldown = false
//									myScroll.refresh()
//								}
//						},20)
						
					myScroll.on('scroll', ()=>{
//						console.log(that.pulldown)
						console.log(myScroll.y)
						if(myScroll.y > 40){
							that.pulldown = true
						}
						if(myScroll.y < myScroll.maxScrollY - 40){
							that.pullup = true
						}
						
						if(that.init === true & that.once === true){
							myScroll.refresh()
							that.once = false
						}
					})
					
					myScroll.on('scrollEnd', ()=>{
						if(that.pulldown == true && that.up){
							that.refresh()
							.then(()=>{
									that.pulldown = false
									myScroll.refresh()
							})
						}
						
						if(that.pullup == true && that.down){
							that.getMore()
							.then(()=>{
								setTimeout(function(){
									that.pullup = false
									myScroll.refresh()
								},10)
							})
						}
					})
					
    }
  }
</script>

<style>
  #wrapper {
	position: absolute;
	z-index: 1;
	top: 45px;
	bottom: 0;
	left: 0;
	width: 100%;
	/*background: #ccc;*/
	overflow: hidden;
}

.msg{
	width:100%;
	position:relative;
	z-index:10;
}

.scroller {
		position:relative;
  	width:90%;
  	display:flex;
  	flex-flow:column;
  	margin:0 auto;
  }
#wrapper .loadmore{
		position:absolute;
  	top:-35px;
  	height:100%;
  	width: 100%;
  	height: 35px;
  	text-align: center;
  	line-height: 35px;
  	color: #bdbdbd;
  }
  #wrapper .loadover{
  	position:absolute;
  	bottom:-35px;
  	width: 100%;
  	height: 35px;
  	text-align: center;
  	line-height: 35px;
  	color: #bdbdbd;
  }
</style>
