<template>
	<view class="root">
		<!-- 表单分析界面 -->
		<view class="root1" style="margin-top: 20rpx;height: 40%;">
			<view class="title">
				<view class="title_bar" >各种表单分析</view>
			</view>
			<view class="anaylise">
				<view v-for="(item,index) in item_tables" wx-key='index' class="docker" @click="clickItem(item)">
					<view class="item">
						<view class="item1" style="width: 30%;">
							<image :src="'../../static/analyse/{{item.index}}.png'" class="imgs"></image>
						</view>
						<view class="item1" style="width: 70%;">
							{{item.name}}
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 已审批表单 -->
		<view class="root1" style="margin-top: 20rpx; height:60%">
			<view class="title">
				<view class="title_bar" >已审批表单</view>
			</view>
			<view class="anaylise">
				<view v-for="(item,index) in check_table" wx-key='index' class="docker" @click="clickItem(item)">
					<view class="item">
						<view class="item1" style="width: 30%;">
							<image :src="'../../static/checked/{{item.index}}.png'" class="imgs"></image>
						</view>
						<view class="item1" style="width: 70%;">
							{{item.name}}
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 添加功能 -->
		<view class="root1" style="margin-top: 20rpx; height: 25%;">
			<view class="title">
				<view class="title_bar" >添加功能</view>
			</view>
			<view class="anaylise">
				<view v-for="(item,index) in add_function" wx-key='index' class="docker" @click="clickItem(item)">
					<view class="item">
						<view class="item1" style="width: 30%;">
							<image :src="'../../static/add/{{item.index}}.png'" class="imgs"></image>
						</view>
						<view class="item1" style="width: 70%;">
							{{item.name}}
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 讨论区功能 -->
		<view class="root1" style="margin-top: 20rpx; height: 25%;">
			<view class="title">
				<view class="title_bar" >讨论区分析</view>
			</view>
			<view class="anaylise">
				<view v-for="(item,index) in talk_function" wx-key='index' class="docker" @click="clickItem(item)">
					<view class="item">
						<view class="item1" style="width: 30%;">
							<image :src="'../../static/talk/{{item.index}}.png'" class="imgs"></image>
						</view>
						<view class="item1" style="width: 70%;">
							{{item.name}}
						</view>
					</view>
				</view>
			</view>
		</view>
		
		

		
	</view>
	
	
</template>

<script>
	import helper from '@/base.js';  
	export default {
		data() {
			return {
				host:helper.host,
				name:'',
				phone:'',
				work:'',
				showDialog: false,
				showPop:false, //弹窗
				shuju:"",
				title:"",
				placeholder:'',
				index:'',
				changeType:'',
				item_tables:[{
					"index":"1",
					"name":'外出申请表单'
				},{
					"index":"2",
					"name":"健康信息表单",
				},{
					"index":"3",
					"name":"物资申请表",
				},{
					"index":"4",
					"name":"外来报备表",
				}],
				check_table:[{
					"index":"1",
					"name":'外出表单已审批'
				},{
					"index":"2",
					"name":"健康信息",
				},{
					"index":"3",
					"name":"物资申请已审批",
				},{
					"index":"4",
					"name":"外来申请已审批",
				},{
					"index":"5",
					"name":"物资发放",
				}],
				add_function:[{
					"index":"1",
					"name":"增加物资"
				},{
					"index":"2",
					"name":"增加管理员"
				}],
				talk_function:[{
					"index":"1",
					"name":"词云分析"
				},{
					"index":"2",
					"name":"热帖分析"
				}],
				userInfo:[],
				deleteInde:'',
			}
		},
		
		onShow() {
			let _this=this
			uni.request({
				url:this.host+'api/admin/getAdmin',
				method:"GET",
				data:{
					
				},
				header:{
					'token':uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==200){
						_this.userInfo=res.data.data
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
			// 点击上部图片事件
			clickItem:function(e){
				var name=e.name
				console.log(name)
				let Json=JSON.stringify(e)
				let data=encodeURIComponent(Json)
				if(name=='外出申请表单'){
					wx.navigateTo({
						url:`../../page2/analyzePage/analyzePage?type=${data}`
					})
				}else if(name=='健康信息表单'){
					wx.navigateTo({
						url:`../../page2/analyzePage/analyzePage?type=${data}`
					})
				}else if(name=='物资申请表'){
					wx.navigateTo({
						url:`../../page2/analyzePage/analyzePage?type=${data}`
					})
				}else if(name=='外来报备表'){
					wx.navigateTo({
						url:`../../page2/analyzePage/analyzePage?type=${data}`
					})
				}else if(name=='词云分析'){
					wx.navigateTo({
						url:`../../page2/analyzePage/analyzePage?type=${data}`
					})
				}else if(name=='增加物资'){
					wx.navigateTo({
						url:`../../page1/addMaterial/addMaterial`
					})
				}else if(name=='增加管理员'){
					wx.navigateTo({
						url:`../../page1/addManager/addManager`
					})
				}else if(name=='热帖分析'){
					uni.showToast({
						title:'未开发',
						duration:1000,
						icon:'error'
					})
				}else if(name=='外出表单已审批'){
					wx.navigateTo({
						url:`../../page2/checkedRequest/checkedRequest?type=${data}`
					})
				}else if(name=='健康信息'){
					wx.navigateTo({
						url:`../../page2/checkedRequest/checkedRequest?type=${data}`
					})
				}else if(name=='物资申请已审批'){
					wx.navigateTo({
						url:`../../page2/checkedRequest/checkedRequest?type=${data}`
					})
				}else if(name=='外来申请已审批'){
					wx.navigateTo({
						url:`../../page2/checkedRequest/checkedRequest?type=${data}`
					})
				}else if(name=='物资发放'){
					wx.navigateTo({
						url:`../../page2/checkedRequest/checkedRequest?type=${data}`
					})
				}
			}
		}
	}
</script>

<style>
page{
	background-color: #FFFFFF;
	width:100%;
	height: 90%;
}
.root{
	width: 100%;
	height: 90%;
	margin-top: 10rpx;
}
.anaylise{
	width: 90%;
	height: 60;
	margin: 0rpx auto;
	box-shadow: 0 0 10rpx #f2f2f2;
}
.docker{
	display: inline-block;
	margin: 0 auto;
	width:50%;
	height: 50%;
	margin:auto 0;
}
.item{
	width: 100%;
	height: 100%;
	text-align: center;
	margin:0 0;
	line-height: 70rpx;
	text-align: center;
	align-items: center;
	border: 1rpx solid #f2f2f2;
	padding: 25rpx 0;
}
.imgs{
	width: 70rpx;
	margin: 0 auto;
	height: 70rpx;
	vertical-align:middle;
	text-align: center;
	margin-top: 10rpx;
}
.item1{
	display: inline-flex;
	width: 48%;
	height: 100rpx;
	line-height: 100rpx;
	vertical-align:middle
}
.title_bar{
	width: 100%;
	height: 100%;
	border-left: 10rpx solid #60befb;
	padding-left: 20rpx;
	margin-bottom: 20rpx;
	line-height: 20rpxrpx;
	font-size: 36rpx;
}
.title{
	width: 90%;
	height:60rpx;
	margin: 0 auto;
	box-shadow: 0 0 10rpx #f2f2f2;
	padding-top: 20rpx;
	padding-bottom: 20rpx;
}
.root1{
	width: 100%;
	height: 60%;
	border-radius: 10rpx;
}
</style>
