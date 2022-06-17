<template>
	<view class="root">
		<view>
			<!-- 互助标题 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">姓名：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getManagetName" class="weui-input" placeholder="请输入管理员姓名" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			<!-- 互助内容 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">联系方式：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getPhone" class="weui-input" placeholder="请输入联系方式" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			
			<!-- 物资价格 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">职责：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getWork" class="weui-input" placeholder="请输入管理员职责" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			
			<!-- 按钮 -->
			<view class="button-sp-area" style="margin-top: 50rpx; margin-bottom: 100rpx; width: 100%;">
				<navigator class="button weui-btn weui-btn_mini weui-btn_primary weui-wa-hotarea" aria-role="button" @click="submit" style="width: 100%;">提交</navigator>
			</view> 
		</view>
	</view>
</template>

<script>
	import helper from '@/base.js';  
	export default {
		data() {
			return {
				files:[],
				index:'0',
				name:'',
				phone:'',
				work:'',
				host:helper.host,
			}
		},
		onLoad() {
			// // this.userId=uni.getStorageSync('userId')
			// this.userId='1'
		},
		
		methods: {
			getManagetName:function(e){
				this.name=e.detail.value
			},
			
			getPhone:function(e){
				this.phone=e.detail.value
			},
			getWork:function(e){
				this.work=e.detail.value
			},
			submit:function(e){
				// 处理图片
				var images=""
				for(var i =0; i<this.files.length;i++){
					  if(i!=this.files.length-2){
						  images=images+this.files[i]
					  }else{
						  images=images+this.files[i]
					  }
				}
				
				
				//提交
				uni.request({
					url:this.host+'api/admin/addAdmin',
					method:"POST",
					data:{
						adminName:this.name,
						adminPhone:this.phone,
						adminWork:this.work,
					},
					header:{
						'token':uni.getStorageSync('token')
					},
					success(res) {
						if(res.data.code==200){
							uni.showToast({
								title:'添加成功',
								duration:1000
							})
						}else{
							uni.showToast({
								title:'添加失败',
								duration:1000,
								icon:"error"
							})
						}
					},
					fail() {
						uni.showToast({
							title:'添加失败',
							duration:1000,
							icon:"error"
						})
					}
				})
			}
		}
	}
</script>

<style>
	page{
		background-color: #fff;
		width: 100%;
		height: 100%;
	}
	.root{
		width: 100%;
		height: 100%;
	}
	.button{
		width: 200rpx;
		padding: 10rpx;
	}
</style>
