<template>
	<view>
		<view class="weui-cell" v-for="(item,index) in typeIndex" wx-key="index">
			<view class="weui-label">{{item}}</view>
			<view class="weui-cell__bd">
				<view class="weui-input">
					{{detailInfos[typeIndexEnglish[index]]}}
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
				tableType:'',
				detailInfos:'',
				typeIndex:'',
				typeIndexEnglish:'',
				host:helper.host,
			};
		},
		onLoad() {
			// 获取表单对应类型和需要展示的数据
			this.tableType=uni.getStorageSync('name')
			this.detailInfos=uni.getStorageSync('detailInfos')
			uni.showLoading({
				title:'数据加载中'
			})
			this.getTypeIndex()
			
		},
		methods:{
			getTypeIndex:function(e){
				//获取表单项对应的索引
				if(this.tableType=="健康信息"){
					this.typeIndex=["姓名","住址","体温","症状","当前地址","最后一次核算","提交时间"]
					this.typeIndexEnglish=['realName',"address","temperature","sympotm","location","lastTime","submit"]
					if(this.detailInfos["sympotm"] == 0){
						this.detailInfos["sympotm"] = "无症状"
					}else if(this.detailInfos["sympotm"] == 1){
						this.detailInfos["sympotm"] = "发烧"
					}else if(this.detailInfos["sympotm"] == 2){
						this.detailInfos["sympotm"] = "咳嗽"
					}
					this.detailInfos.temperature=this.detailInfos.temperature+'℃'
					uni.hideLoading()
				}else if(this.tableType=="外出申请"){
					this.typeIndex=["姓名","住址","外出时间","回来时间","外出方式","目的地","外出原因","申请时间","审批人","审批结果","审批时间"]
					this.typeIndexEnglish=["applicantName","address","leaveTime","backTime","way","destination","reason","submitDate","approverId","result","approverTime"]
					if(this.detailInfos.result==null){
						this.detailInfos.result='未审批'
						this.detailInfos.approverTime='未审批'
					}else if(this.detailInfos.result==1){
						this.detailInfos.result="已通过"
					}else if(this.detailInfos.result==-1){
						this.detailInfos.result="未通过"
					}
					// 修改外出方式
					uni.request({
						url:this.host+'api/getByTypeAndId',
						data:{
							type:'出行方式',
							id:this.detailInfos.way
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								this.detailInfos.way=res.data.data
							}else{
								uni.showToast({
									title:'加载出行方式失败',
									duration:1000,
									icon:"error"
								})
							}
						},
						fail() {
							uni.showToast({
								title:'加载出行方式失败',
								duration:1000,
								icon:"error"
							})
						}
					})
				}else if(this.tableType=="物资申请已审批"){
					this.typeIndex=["姓名","住址","物资名称","申请数量","单价","总费用","是否紧急","申请原因","审批人","申请时间","审批时间","审批结果"]
					this.typeIndexEnglish=["applicantName","address","materialName","materialNum","materialPrice","materialAllPrice","urgent","reason","approverId","submitDate","approverTime","result"]
					if(this.detailInfos['result']==0){
						this.detailInfos['result'] = "待审批"
						this.detailInfos['approverTime'] = "待审批"
					}else if(this.detailInfos['result']==1){
						this.detailInfos['result'] = "已通过"
					}else if(this.detailInfos['result']==-1){
						this.detailInfos['result']='未通过'
					}
					if(this.detailInfos['urgent']==0){
						this.detailInfos['urgent']='不紧急'
					}else{
						this.detailInfos['urgent']='紧急'
					}
					if(this.detailInfos.ifApprovered==0){
						this.detailInfos.ifApprovered='未审批'
					}else if(this.detailInfos.ifApprovered==1){
						this.detailInfos.ifApprovered='已审批'
						this.detailInfos['result']='已通过'
					}else if(this.detailInfos.ifApprovered==-1){
						this.detailInfos.ifApprovered='不通过'
						this.detailInfos['result']='未通过'
					}
				}else if(this.tableType=="外来人员报备"){
					this.typeIndex=["姓名","身份证号","电话","来前住址","来后住址","来往方式","申请时间","审批人",'审批结果']
					this.typeIndexEnglish=["name","idNum","phone","location","address","way","submitDate","approverId",'ifApprovered']
					if(this.detailInfos.ifApprovered==0){
						this.detailInfos.ifApprovered='未审批'
					}else if(this.detailInfos.ifApprovered==1){
						this.detailInfos.ifApprovered='已审批'
					}else if(this.detailInfos.ifApprovered==-1){
						this.detailInfos.ifApprovered='不通过'
					}
					
					// 修改来往方式
					uni.request({
						url:this.host+'api/getByTypeAndId',
						data:{
							type:'来往方式',
							id:this.detailInfos.way
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							if(res.data.code==200){
								this.detailInfos.way=res.data.data
								
							}else{
								uni.showToast({
									title:'加载来往方式失败',
									duration:1000,
									icon:"error"
								})
							}
						},
						fail() {
							uni.showToast({
								title:'加载来往方式失败',
								duration:1000,
								icon:"error"
							})
						}
					})
					
				}else if(this.tableType=='已获批物资'){
					this.typeIndex=["姓名","住址","物资名称","申请数量","单价","总费用","是否紧急","申请原因","审批人","申请时间","审批时间","审批结果"]
					this.typeIndexEnglish=["applicantName","address","materialName","materialNum","materialPrice","materialAllPrice","urgent","reason","approverId","submitDate","approverTime","result"]
					if(this.detailInfos['result']=="0"){
						this.detailInfos['result'] = "待审批"
						this.detailInfos['approverTime'] = "待审批"
					}else{
						this.detailInfos['result'] = "已通过"
					}
					if(this.detailInfos['urgent']==0){
						this.detailInfos['urgent']='不紧急'
					}else{
						this.detailInfos['urgent']='紧急'
					}
				}
				if(this.tableType!="健康信息"){
					// 修改审批人
					uni.request({
						url:this.host+'api/getByTypeAndId',
						data:{
							type:'审批人',
							id:this.detailInfos.approverId
						},
						header:{
							'token':uni.getStorageInfoSync('token')
						},
						success: (res) => {
							console.log('’'+res)
							if(res.data.code==200){
								this.detailInfos.approverId=res.data.data
								uni.hideLoading()
							}else{
								uni.showToast({
									title:'加载管理员失败',
									duration:1000,
									icon:"error"
								})
							}
						},
						fail() {
							uni.showToast({
								title:'加载管理员失败',
								duration:1000,
								icon:"error"
							})
						}
					})
				}
				
				
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
</style>
