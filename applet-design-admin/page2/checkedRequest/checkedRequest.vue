<template>
	<view class="root">
		<!--内容显示区域-->
		<view v-for="(items, index) in userDatas" :key="index">
				<husmalltableOut :reciveUserItemIndex="reciveUserItemIndex" :reciveUserItem='reciveUserItem' :userData='items' @backApplication='backApplication' ></husmalltableOut>
		</view>		
	</view>
</template>

<script>
	import husmalltableOut from "@/components/checked.vue";
	import helper from '@/base.js';  
	export default {
		components:{
			husmalltableOut
		},
		data() {
			return {
				userDatas:'',
				host:helper.host,
				reciveUserItem:'',
				reciveUserItemIndex:'',
				name:''
			};
		},
		onLoad(options) {
			// 获取页面传值
			let data=options.type
			let jsons=decodeURIComponent(data)
			let json=JSON.parse(jsons)
			this.index=json.index
			this.name=json.name
			var name=this.name
			var getMethod=''
			
			if(name=='物资发放'){
				this.reciveUserItem = ['姓名','申请物资名称','住址','是否紧急','申请时间']
				this.reciveUserItemIndex=['applicantName','materialName','address','urgent','submitDate']
			}else if(name=='健康信息'){
				this.reciveUserItem = ['姓名','体温','住址','最后一次核算时间','提交时间']
				this.reciveUserItemIndex=['realName',"temperature","address","lastTime","submit"]
			}else if(name=='外来申请已审批'){
				this.reciveUserItem=['姓名','审批人','现住址','原住址','申请时间']
				this.reciveUserItemIndex=['name','approverId','address','location','submitDate']
			}else if(name=='外出表单已审批'){
				this.reciveUserItem = ['姓名','外出地点','住址','外出原因','申请时间']
				this.reciveUserItemIndex=['applicantName','destination','address','reason','submitDate']
			}else if(name=='物资申请已审批'){
				this.reciveUserItem = ['姓名','申请物资名称','住址','是否紧急','申请时间']
				this.reciveUserItemIndex=['applicantName','materialName','address','urgent','submitDate']
			}
			
			uni.showLoading({
				title:'数据加载中'
			})
			
			// 定义表头
			if(name=='外出表单已审批'){
				getMethod='api/leave/getAllAccessedByApproverId'
			}else if(name=='健康信息'){
				getMethod='api/table/getHealthInfoAllById'
			}else if(name=='物资申请已审批'){
				getMethod='api/material/getMaterialByApproverId'
			}else if(name=='外来申请已审批'){
				getMethod='api/report/getReportByApproverId'
			}else if(name=='物资发放'){
				getMethod='api/table/getMaterialAlreadyInfoAllById'
			}
			let _this=this
			uni.request({
				url:_this.host+getMethod,
				data:{
					// id:uni.getStorageSync('user_id')
					id:1
				},
				header:{
					token:uni.getStorageSync('token')
				},
				success: (res) => {
					if(res.data.code==200){
						if(_this.name=='外来申请已审批'){
							_this.userDatas=res.data.data[0].main // 进行赋值
							for(var i =0;i<_this.userDatas.length;i++){
								_this.userDatas[i].approverId=res.data.data[1].names[i]
							}
							uni.hideLoading()
						}else{
							_this.userDatas=res.data.data
							uni.hideLoading()
						}
					}
				}
			})
			
			
		},
		methods:{
			backApplication:function(e){
				console.log(e)
				let _this = this
				var names=this.name
				var name = ''
				var getMethod = ""
				if(names=='健康信息'){
					getMethod = "table/getHealthInfoById"
					name='健康信息'
				}else if(names=='物资申请已审批'){
					getMethod = "table/getMaterialInfoById"
					name='物资申请已审批'
				}else if(names=='物资发放'){
					getMethod = "table/getMaterialInfoById"
					name='已获批物资'
				}else if(names=='外来申请已审批'){
					getMethod = "table/getReportById"
					name='外来人员报备'
				}else if(names=='外出表单已审批'){
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
							
							wx.navigateTo({
								url:'../../pages3/details/details'
							})
						}
					}
				})
				
				
				
			},
		}
	}
</script>

<style lang="scss">
	page{
		width: 100%;
		height: 100%;
		background-color: #ffffff;
	}
	root{
		width: 90%;
		height: 100%;
	}
</style>
