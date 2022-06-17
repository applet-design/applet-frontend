<template>
	<view class="root">
		<!-- 顶部导航栏 -->
		<view class="horizonal-tab">
			<scroll-view scroll-x="true" scroll-with-animation class="scroll-tab">
				<block v-for="(item,index) in tabBars" :key="index">
					<view class="scroll-tab-item" :class="{'active': tabIndex==index}" 
					@tap="toggleTab(index)">
						{{item.name}}
						<view class="scroll-tab-line"></view>
					</view>
				</block>
			</scroll-view>
		</view>
		
		<view class="search_area">
			<view class="search_bar">
				<view class="search_select search" style="border-right: 4rpx solid #CCCCCC;">
					<picker aria-role="combobox" @change="bindSearchPicker" :value="searchPickerIndex" :range="searchPicker">
						<input style="padding-left: 50rpx; height: 2em;" class="weui-input" placeholder="选择类型" placeholder-class="weui-input_placeholder" disabled :value="searchIfChoose"/>
					</picker>
				</view>
				<view class="search_info search" style="padding: 0rpx;">
					<view class="search_select search" style="width: 100%;padding: 0rpx;">
						<picker aria-role="combobox" @change="search" :value="serchInfoIndex" :range="searchInfo">
							<input style="padding-left: 50rpx;height: 2em;" class="weui-input" placeholder-class="weui-input_placeholder" placeholder="选择内容" disabled :value="searchInfoChoose" />
						</picker>
					</view>
				</view>
			</view>
		</view>
		
		<view :class="show==false?'noshow':'show'">
			<image src="../../static/no_data.png" style="width: 100rpx; height: 100rpx;" ></image>
			<view>暂无数据</view>
		</view>
		
		<!--内容显示区域-->
		<view v-for="(items, index) in userDatas" :key="index">
				<husmalltableOut :reciveUserItemIndex="reciveUserItemIndex" :reciveUserItem='reciveUserItem' :userData='items' @backApplication='backApplication' @lookDetails='lookDetails'></husmalltableOut>
		</view>		
		
		
	</view>
	


</template>

 
<script>
	import husmalltableOut from "@/components/hu-small-table1/hu-small-table1.vue";
	import helper from '@/base.js';  
	export default {
		components:{
			husmalltableOut
		},
		data () {
			return {
				tabIndex: 0, /* 选中标签栏的序列,默认显示第一个 */
				show:'',
				userDatas:'',
				host:helper.host,
				contentList: [
					"生活必须物资申请表单",
					"人员健康信息填报表",
					"小区外来人员报备表",
					'出小区申请表',
				],
				reciveUserItem:'',
				reciveUserItemIndex:'',
				tabBars:[
					{
						name: '生活必须物资申请表单',
						id: 'material_application'
					},
					{
						name: '人员健康信息填报表',
						id: 'info'
					},
					{
						name: '小区外来人员报备表',
						id: 'report'
					},
					{
						name: '出小区申请表',
						id: 'application'
					},
				],
				searchInfoChoose:'',
				searchIfChoose:'',
				searchPickerIndex:[0,1,2],
				searches:'',
				searchPicker:['住址','表单类型','体温'],
				searchInfo:'',
				serchInfoIndex:[0,1],
				searchInfo_one:'筛选条件1',
				searchInfo_two:'筛选条件2',
				searchInfo_three:'筛选条件3',
			}
		},
		
		onLoad() {
			uni.setStorage({
				data:this.tabBars[this.tabIndex].name,
				key:'tabBarName'
			})
			
			let name=uni.getStorageSync('tabBarName')
			console.log(name)
			
			if(name=='生活必须物资申请表单'){
				this.reciveUserItem = ['姓名','申请物资名称','住址','是否紧急','申请时间']
				this.reciveUserItemIndex=['applicantName','materialName','address','urgent','submitDate']
			}else if(name=='人员健康信息填报表'){
				this.reciveUserItem = ['姓名','体温','住址','最后一次核算时间','提交时间']
				this.reciveUserItemIndex=['realName',"temperature","address","lastTime","submit"]
			}else if(name=='小区外来人员报备表'){
				this.reciveUserItem=['姓名','审批人','现住址','原住址','申请时间']
				this.reciveUserItemIndex=['name','approverId','address','location','submitDate']
			}else if(name=='外出小区申请表'){
				this.reciveUserItem = ['姓名','外出地点','住址','外出原因','申请时间']
				this.reciveUserItemIndex=['applicantName','destination','address','reason','submitDate']
			}
			
			
			uni.showLoading({
				title:'数据加载中'
			})
			
			let _this=this
			// 获取默认数据
			uni.request({
				url:_this.host+'api/table/getMaterialInfoAllById',
				data:{
					// 'approverId':uni.getStorageInfoSync('approverId'),
					'approverId':1,
					'ifApproverId':0
				},
				header:{
					'token':uni.getStorageInfoSync('token')
				},
				success: (res) => {
					if(res.data.code==200){
						console.log('查询成功')
						if(res.data.data==''){
							_this.show=false
						}else{
							_this.show=true
						}
						_this.userDatas=res.data.data // 进行赋值
					}else{
						console.log('查询失败')
					}
				},
				fail: (res) => {
					console.log('查询失败111')
				}
			})
			
			uni.request({
				url:_this.host+'api/getSearch',
				data:{
					// 'approverId':uni.getStorageInfoSync('approverId'),
					type:'物资申请'
				},
				header:{
					'token':uni.getStorageInfoSync('token')
				},
				success: (res) => {
					if(res.data.code==200){
						console.log(res)
						_this.searchPicker=res.data.data[0].main
						_this.searches=res.data.data[1].names
					}else{
						console.log('查询失败')
					}
				},
				fail: (res) => {
					console.log('查询失败')
				}
			})
			
			uni.hideLoading();
		},
		
		methods:{
			search:function(e){
				// 先是赋值
				this.searchInfoChoose=this.searchInfo[e.detail.value]
				// 判断是否满足搜索条件
				if(this.searchInfo=="" && this.searchIfChoose==""){
					return ;
				}
				
				let names=uni.getStorageSync('tabBarName')
				var name= this.searchInfoChoose
				console.log(this.searchInfoChoose)
				let _this=this
				if(names=='生活必须物资申请表单'){
					if(this.searchIfChoose=='物资名称'){
						// 根据物资名称获取物资
						uni.request({
							url:_this.host+'api/material/getByName',
							method:"GET",
							data:{
								name:name,
								approverId:1,
								ifApp:0
							},
							header:{
								'token':uni.getStorageSync('token')
							},
							success(res) {
								if(res.data.code==200){
									if(res.data.data==''){
										_this.show=false
									}else{
										_this.show=true
									}
									_this.userDatas=res.data.data
								}
							}
						})
					}else if(this.searchIfChoose=='是否紧急'){
						var a='';
						if(this.searchInfoChoose=='是'){
							a=1
						}else{
							a=0
						}
						// 根据物资名称获取物资
						uni.request({
							url:this.host+'api/material/getByUrgent',
							method:"GET",
							data:{
								a:a,
								approverId:1,
								ifApp:0,
							},
							header:{
								'token':uni.getStorageSync('token'),
								
							},
							success(res) {
								if(res.data.code==200){
									if(res.data.data==''){
										_this.show=false
									}else{
										_this.show=true
									}
									_this.userDatas=res.data.data
								}
							}
						})
					}
				}else if(names=='人员健康信息填报表'){
					if(this.searchInfoChoose=='体温异常'){
						uni.request({
							url:this.host+'api/tem/nonormal',
							method:"GET",
							data:{
								
							},
							header:{
								'token':uni.getStorageSync('token'),
								
							},
							success(res) {
								if(res.data.code==200){
									if(res.data.data==''){
										_this.show=false
									}else{
										_this.show=true
									}
									_this.userDatas=res.data.data
								}
							}
						})
					}else if(this.searchInfoChoose=='体温正常'){
						uni.request({
							url:this.host+'api/tem/normal',
							method:"GET",
							data:{
							
							},
							header:{
								'token':uni.getStorageSync('token'),
								
							},
							success(res) {
								if(res.data.code==200){
									if(res.data.data==''){
										_this.show=false
									}else{
										_this.show=true
									}
									_this.userDatas=res.data.data
								}
							}
						})
					}
				}else if(names=="小区外来人员报备表"){
					var add=_this.searchInfoChoose
					uni.request({
						url:_this.host+'api/report/getAdd',
						method:"GET",
						data:{
							add:add
						},
						header:{
							'token':uni.getStorageSync('token'),
							
						},
						success(res) {
							if(res.data.code==200){
								if(res.data.data==''){
									_this.show=false
								}else{
									_this.show=true
								}
								_this.userDatas=res.data.data
							}
						}
					})
				}else if(names=='出小区申请表'){
					var add=_this.searchInfoChoose
					uni.request({
						url:_this.host+'api/leave/getAdd',
						method:"GET",
						data:{
							add:add
						},
						header:{
							'token':uni.getStorageSync('token'),
							
						},
						success(res) {
							if(res.data.code==200){
								if(res.data.data==''){
									_this.show=false
								}else{
									_this.show=true
								}
								_this.userDatas=res.data.data
							}
						}
					})
				}

			},
			
			clear:function(e){
				this.searchIfChoose=""
				this.searchInfoChoose=""
			},
			
			//切换选项卡
			toggleTab:function(index){ 
				this.searchIfChoose=""
				this.searchInfoChoose=""
				this.tabIndex=index;
				uni.setStorage({
					data:this.tabBars[this.tabIndex].name,
					key:'tabBarName'
				})
				
				let name=uni.getStorageSync('tabBarName')
				console.log(name)
				if(name=='生活必须物资申请表单'){
					this.reciveUserItem = ['姓名','申请物资名称','住址','是否紧急','申请时间']
					this.reciveUserItemIndex=['applicantName','materialName','address','urgent','submitDate']
				}else if(name=='人员健康信息填报表'){
					this.reciveUserItem = ['姓名','体温','住址','最后一次核算时间','提交时间']
					this.reciveUserItemIndex=['realName',"temperature","address","lastTime","submit"]
				}else if(name=='小区外来人员报备表'){
					this.reciveUserItem=['姓名','审批人','现住址','原住址','申请时间']
					this.reciveUserItemIndex=['name','approverId','address','location','submitDate']
				}else if(name=='出小区申请表'){
					this.reciveUserItem = ['姓名','外出地点','住址','外出原因','申请时间']
					this.reciveUserItemIndex=['applicantName','destination','address','reason','submitDate']
				}
				
				var api='';
				var postData='';
				
				if(index==0){
					api='api/table/getMaterialInfoAllById'
				}else if(index==1){
					api='api//table/getHealthInfoAllById'
				}else if(index==2){
					api='api/table/getReportAllById'
				}else if(index==3){
					api='api/table/getLeaveAllInfo'
				}
				
				
				
				
				
				uni.showLoading({
					title:'数据加载中'
				})
				
				let _this=this
				
				if(index==2){
					// 获取默认数据
					uni.request({
						url:_this.host+api,
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							'approverId':1,
							'ifApproverId':0
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log('查询成功')
								if(res.data.data[0].main==''){
									_this.show=false
								}else{
									_this.show=true
								}
								_this.userDatas=res.data.data[0].main // 进行赋值
								for(var i =0;i<_this.userDatas.length;i++){
									_this.userDatas[i].approverId=res.data.data[1].names[i]
								}
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
					
					uni.hideLoading();
				}else{
					// 获取默认数据
					uni.request({
						url:_this.host+api,
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							'approverId':1,
							'ifApproverId':0
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log('查询成功')
								if(res.data.data==''){
									_this.show=false
								}else{
									_this.show=true
								}
								_this.userDatas=res.data.data // 进行赋值
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
					
					uni.hideLoading();
				}
				
				if(index==0){
					uni.request({
						url:_this.host+'api/getSearch',
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							type:'物资申请'
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log(res)
								_this.searchPicker=res.data.data[0].main
								_this.searches=res.data.data[1].names
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
				}else if(index==1){
					uni.request({
						url:_this.host+'api/getSearch',
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							type:'健康信息'
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log(res)
								_this.searchPicker=res.data.data[0].main
								_this.searches=res.data.data[1].names
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
				}else if(index==2){
					uni.request({
						url:_this.host+'api/getSearch',
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							type:'外来人员'
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log(res)
								_this.searchPicker=res.data.data[0].main
								_this.searches=res.data.data[1].names
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
				}else if(index==3){
					uni.request({
						url:_this.host+'api/getSearch',
						data:{
							// 'approverId':uni.getStorageInfoSync('approverId'),
							type:'外出申请'
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								console.log(res)
								_this.searchPicker=res.data.data[0].main
								_this.searches=res.data.data[1].names
							}else{
								console.log('查询失败')
							}
						},
						fail: (res) => {
							console.log('查询失败')
						}
					})
				}
				
			
				
				
			},
			//滑动切换swiper
			tabChange:function(e) { 
				this.tabIndex=e.detail.current;
				
			},
			bindSearchPicker:function(e){
				console.log(e)
				this.searchIfChoose=this.searchPicker[e.detail.value]
				this.searchInfo=this.searches[e.detail.value]

			},
			bindSearchInfo:function(e){
				this.searchInfoChoose=this.searchInfo[e.detail.value]
				
			},
			backApplication:function(e){
				console.log(e)
				let _this = this
				var names=this.tabIndex
				var name = ''
				var getMethod = ""
				if(names==0){
					getMethod = "table/getMaterialInfoById"
					name='物资申请'
				}else if(names==1){
					getMethod = "table/getHealthInfoById"
					name='健康信息'
				}else if(names==2){
					getMethod = "table/getReportById"
					name='外来人员报备'
				}else if(names==3){
					getMethod = "table/getLeaveInfo"
					name='外出申请'
				}
				uni.setStorage({
					data:name,
					key:'name'
				})
				
				uni.request({
					url:_this.host+'api/'+getMethod,
					method:"GET",
					data:{
						applicantId:e,
					},
					header:{
						'token':uni.getStorageInfoSync('token')
					},
					success(res) {
						if(res.data.code!=200){
							uni.showToast({
								title:'获取详细信息失败',
								duration:2000
							}),
							_this.detailInfos=''
						}else{
							_this.detailInfos=res.data.data
							console.log(_this.detailInfos)
							uni.setStorage({
								key:'detailInfos',
								data:_this.detailInfos
							})
						}
					}
				})
				
				
				wx.navigateTo({
					url:'../../pages3/details/details'
				})
			},
			lookDetails:function(e){
				let _this=this
				let tabBarName=uni.getStorageSync('tabBarName')
				if(tabBarName=='人员健康信息填报表'){
					uni.showToast({
						title:'不能审批',
						duration:1000,
						icon:"error"
					})
					return 
				}
				
				var api='';
				if(tabBarName=='生活必须物资申请表单'){
					api='api/table/accessMaterialById'
				}else if(tabBarName=='小区外来人员报备表'){
					api='api/table/accessReportById'
				}else if(tabBarName=='出小区申请表'){
					api='api/table/accessLeaveById'
				}
				
				uni.showActionSheet({
					itemList:['通过','不通过'],
					success:function(res) {
						if(res.tapIndex==0){
							// 通过
							uni.request({
								url:_this.host+api,
								data:{
									applicantId:e,
									'access':1,
									// approverId:uni.getStorageSync('approverId')
									approverId:1,
									result:1
								},
								header:{
									'token':uni.getStorageInfoSync('token')
								},
								success(res) {
									if(res.data.code==200){
										uni.showToast({
											title:'审批成功',
											duration:1000
										})
										if(uni.getStorageSync('tabBarName')=='小区外来人员报备表'){
											_this.userDatas=res.data.data[0].main // 进行赋值
											for(var i =0;i<_this.userDatas.length;i++){
												_this.userDatas[i].approverId=res.data.data[1].names[i]
											}
										}else{
											_this.userDatas=res.data.data
										}
										if(_this.userDatas==''){
											_this.show=false
										}else{
											_this.show=true
										}
										
										uni.request({
										url: _this.host+'api/sendMessage' ,//调用后端发送订阅消息接口,
										data:{
											applicantId:e,
											type:uni.getStorageSync('tabBarName')
										}
										})
										
										
										
									}else{
										uni.showToast({
											title:'审批出错',
											duration:1000,
											icon:"error"
										})
									}
								},
								fail: (res) => {
									uni.showToast({
										title:'审批出错',
										duration:1000,
										icon:"error"
									})
								}
							})
						}else if(res.tapIndex==1){
							//不通过
							uni.request({
								url:_this.host+api,
								data:{
									applicantId:e,
									'access':-1,
									// approverId:uni.getStorageSync('approverId')
									approverId:1,
									result:-1
								},
								header:{
									'token':uni.getStorageInfoSync('token')
								},
								success(res) {
									if(res.data.code==200){
										uni.showToast({
											title:'不通过',
											duration:1000
										})
										if(uni.getStorageSync('tabBarName')=='小区外来人员报备表'){
											_this.userDatas=res.data.data[0].main // 进行赋值
											for(var i =0;i<_this.userDatas.length;i++){
												_this.userDatas[i].approverId=res.data.data[1].names[i]
											}
										}else{
											_this.userDatas=res.data.data
										}
										if(_this.userDatas==''){
											_this.show=false
										}else{
											_this.show=true
										}
										
										uni.request({
										url: _this.host+'api/sendMessage' ,//调用后端发送订阅消息接口,
										data:{
											applicantId:e,
											type:uni.getStorageSync('tabBarName')
										}
										})
										
									}else{
										uni.showToast({
											title:'审批成功',
											duration:1000,
											icon:"error"
										})
									}
								},
								fail: (res) => {
									uni.showToast({
										title:'审批出错',
										duration:1000,
										icon:"error"
									})
								}
							})
						}
					}
				})
			}
		}
	}
	
</script>
 
<style>
	page{
		background-color: white;
		width: 100%;
		height: 100%;
	}
	.root{
		width: 100%;
		height: 100%;
		margin-top:10rpx;
	}
	.search-area{
		height: 50rpx;
		background-color: #007AFF;
		width: 100%;
		
	}
	picker{
		height: 100%;
		line-height: 80rpx;
	}
	picker input{
		height:20px;
		min-height:0.8rem;
	}
	.search_bar{
		width: 100%;
		height:80rpx;
		display: inline-flex;
		border-bottom: 1px solid #CCCCCC;
		overflow: hidden;
	}
	.search{
		width: 49%;
		height: 100%;
		padding: 10rpx;
	}
	.search_button{
		display: inline-flex;
		margin-top: 8rpx;
	}
	.search_select{
		
	}
	.button{
		margin-left: 10rpx;
		height: 60rpx;
		box-shadow: 0 0 5px #CCCCCC;
		width: 150rpx;
		border-radius: 0rpx;
		height: 65rpx;
		text-align: center;
		line-height: 65rpx;
		background-color: white;
		font-size: 28rpx;
		font-family: '微软雅黑';
	}
	.uni-input{
		border-right: 1px solid #000000;
		
	}
	.output{
		margin-left: 10rpx;
	}
	.showInfo{
		height: 200rpx;
		width: 100%;
		color: #000;
		text-align: center;
		border: 1px soild #000000;
		box-shadow: 0 0 5rpx #C0C0C0;
		line-height: 200rpx;
	}
	.horizonal-tab{
		height: 110rpx;
		line-height: 110rpx;
		background-color: white;
		font-size: 32rpx;
		font-family: '宋体';
		font-weight: bold;
	}
	.horizonal-tab .active{
		color: red;
		font-size: 36rpx;
	}
	.scroll-tab{
		white-space: nowrap; /* 必要，导航栏才能横向*/
		text-align: center;
	}
	.scroll-tab-item{
		display: inline-block; /* 必要，导航栏才能横向*/
		margin: 0 0 0 30rpx;
	}
	.active .scroll-tab-line{
		border-top: 5rpx solid red;
		border-radius: 20rpx;
		width: 100%;
		
		
	}
	.table{
		display: inline-flex;
		height: 70rpx;
		line-height: 70rpx;
		width:150rpx;
		text-align: center;
		
	}
	.uni-input{
		width: 100%;
	}
	.button1{
		position: absolute;
		right: 20px;
		height: 70rpx;
		line-height: 70rpx;
		margin: 0rpx;
		border: none;
	}
	.searchBar{
		margin: 0rpx;
		padding: 0rpx;
		font-size: 26rpx;
		text-align: center;
	}
	.form{
		padding: 0rpx;
		margin: 0rpx;
		border: 1px soild #000;
	}
	.show{
		display: none;
	}
	.noshow{
		display: flex;
		justify-content: center; /** flex 属性， 水平居中**/
		width: 100%; 
		height: 100%; 
		align-items: center;
	}
</style>