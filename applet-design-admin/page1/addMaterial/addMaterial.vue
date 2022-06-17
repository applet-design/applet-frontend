<template>
	<view class="root">
		<view>
			<!-- 互助标题 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">物资名称：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getmaterialName" class="weui-input" placeholder="请输入物资名称 10 字以内" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			<!-- 互助内容 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">物资数量：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getmaterialNum" class="weui-input" placeholder="请输入物资数量" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			
			<!-- 物资价格 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">物资单价：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<input @input="getmaterialPrice" class="weui-input" placeholder="请输入物资单价" placeholder-class="weui-input_placeholder" />
					</view>
				</view>
			</view>
			
			<!-- 物资类型 -->
			<view class="weui-cells" style="padding: 0; margin-top: 0;">
				<view class="weui-cell weui-cell_active">
					<view class="weui-label">物资类型：
						<label style="color: red;">*</label>
					</view>
					<view class="weui-cell__bd">
						<picker mode="selector"  :range='typeValue' :value="typeIndex" @change="getType">
							{{materialType}}
						</picker>
					</view>
				</view>
			</view>
			
			<!-- 上传图片 -->
			<view class="weui-cells" style="margin-top: 0;">
				<view class="weui-cell weui-cell_uploader">
					<view class="weui-cell__bd">
						<view class="weui-uploader">
							<view class="weui-uploader__hd">
								<view aria-role="option" class="weui-uploader__overview">
									<view class="weui-uploader__title">图片上传</view>
									<view class="weui-uploader__info">{{files.length}}</view>
								</view>
							</view>
							
							<view class='weui-uploader__bd'>
								<view class='weui-uploader__files' id="uploaderFiles">
									<block v-for="(item,index) in files" :key="index">
										<view class='weui-uploader__file' @click="previewItem" :id="item">
											<image class="weui-uploader__img" :src="item" mode="aspectFill" />
										</view>
									</block>
								</view>
								<view class='weui-uploader__input-box'>
									<view aria-role='button' aria-label='上传' class='weui-uploader__input' @click="chooseImage"></view>
								</view>
							</view>
						</view>
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
				materialName:'',
				materialNum:'',
				materialPrice:'',
				materialType:'水果',
				tabindex:0,
				typeIndex:[0,1,2],
				typeValue:['水果','药品','生活必需品'],
				host:helper.host,
				i:'data:image/png;base64,'
			}
		},
		onLoad() {
			// this.userId=uni.getStorageSync('userId')
			this.userId='1'
		},
		
		methods: {
			getmaterialName:function(e){
				this.materialName=e.detail.value
			},
			
			getmaterialNum:function(e){
				this.materialNum=e.detail.value
			},
			getmaterialPrice:function(e){
				this.materialPrice=e.detail.value
			},
			getType:function(e){
				console.log(e.detail.value)
				this.tabindex=e.detail.value
				this.materialType=this.typeValue[e.detail.value]
			},
			chooseImage:function(e){
				let that = this;
				wx.chooseImage({
				  sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
				  sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
				  success(res) {
					that.files[that.index]=res.tempFilePaths[0]
					that.index=that.index+1
					wx.uploadFile({
						url:that.host+'api/image',
						filePath:that.files[0],
						header:{"Content-Type": "multipart/form-data"},
						name:'file',
						formData:{
							'user':'test'
						},
						success(ress){
							that.i=that.i+ress.data
						},
						fail(res){
							console.log(res)
						}
					})
				  },
				});
			},
			
			previewItem:function(e){
				wx.previewImage({
				  current: e.currentTarget.id, // 当前显示图片的http链接
				  urls: this.files, // 需要预览的图片http链接列表
				});
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
					url:this.host+'api/addMaterial',
					method:"POST",
					data:{
						materialName:this.materialName,
						materialNum:this.materialNum,
						materialImg:this.i,
						materialPrice:this.materialPrice,
						materialType:this.tabindex
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
