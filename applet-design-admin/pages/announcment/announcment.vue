<template>
	<view class="root">
		  <!-- 顶部导航区域 -->
		  <view class="horizonal-tab">
		  	<view class="tab-item"  v-for="(itemName,index) in tabs" :key="index" :class="index == current ? 'nowTab' : 'elseTab'"  @click="changeCurrentTab(index)">{{itemName}}</view>
		  </view>
		  <view v-for="(userDatas,index) in userData" :key='index'>
		  	<husmalltableOut :reciveUserItemIndex="reciveUserItemIndex" :reciveUserItem='reciveUserItem' :userData='userDatas' @backApplication='backApplication' @lookDetails='lookDetails'></husmalltableOut>
		  </view>
		  
		
		 
		<countryInfo :class = "{'show' :current==0,'notShow':current == 1}"></countryInfo>
		<communityInfo :class = "{'show' :current==1,'notShow':current == 0}"></communityInfo>
			

	</view>
</template>

<script>
	
	import countryInfo from "components/countryInfo.vue";
	import communityInfo from "components/communityInfo.vue";
	
	export default {
		components:{
			countryInfo,
			communityInfo
		},
		
		data() {
			return {
				    tabs: ['全国疫情通知','小区疫情通知'],
					host:'http://127.0.0.1:8080/',
				    current:0,	 
					add_class:'',
					tab_name:'',
			}
		},
		onReady() {
			var userName=''
			// 获取用户名，并存于内存中
			uni.setStorage({
				data:1,
				key:"userId"
			})
			// 根据管理元ID查询管理员姓名
			uni.request({
				url:this.host+'api/getByTypeAndId',
				method:"GET",
				data:{
					type:"审批人",
					id:uni.getStorageSync('userId')
				},
				header:{
					'token':uni.getStorageSync('token')
				},
				success: (res) => {
					if(res.data.code==200){
						userName=res.data.data
						uni.setStorage({
							data:userName,
							key:"username"
						})
						console.log(uni.getStorageSync('username'))
					}else{
						uni.showToast({
							title:'加载失败',
							duration:1000,
							icon:"error"
						})
					}
				},
				fail() {
					uni.showToast({
						title:'加载失败',
						duration:1000,
						icon:"error"
					})
				}
			})
		},
		methods: {
			changeCurrentTab:function(e){
				let _this=this
				_this.current =e
				_this.tab_name=_this.tabs[_this.current]
			}
		}
	}
</script>

<style>
.show{
	display: block;
}
.notShow{
	display: none;
}
page{
	background-color: #FFFFFF;
	width: 100%;
	height: 95%;
}
.root{
	width: 100%;
	height: 100%;
	margin-top: 20rpx;
}
.horizonal-tab{
	display: flex;
	justify-content: space-around;
	height: 7%;
}
.tab-item{
	line-height:100%;
	flex: 1;
	text-align: center;
	font-size: 28rpx;
	font-family: '宋体';
	font-weight: bold;
}
.nowTab{
	display: block;
	border-bottom: 3px solid red;
	color: red;
	font-size: 32rpx;
}

</style>
