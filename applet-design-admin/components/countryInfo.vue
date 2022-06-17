<template>
<view>
		<view class="weui-form" style="padding-top: 10rpx; border-radius: 10rpx; box-shadow: 0 0 5rpx #CCCCCC;">
			<!-- 小区疫情通知 -->
			<!-- 通知标题 -->
			<view class="weui-cell weui-cell_active">
				<view class="weui-label">通知标题：
					<label style="color: red;">*</label>
				</view>
				<view class="weui-cell__bd">
					<input @input="getTitle" class="weui-input" placeholder="请输入通知标题" />
				</view>
			</view>
			<!-- 本地新增 -->
			<view class="weui-cell weui-cell_active">
				<view class="weui-label">本地新增：
					<label style="color: red;">*</label>
				</view>
				<view class="weui-cell__bd">
					<input @input="getLocal" class="weui-input" placeholder="本地新增感染人数" />
				</view>
			</view>
			<!-- 本省新增 -->
			<view class="weui-cell weui-cell_active">
				<view class="weui-label">本省新增：
					<label style="color: red;">*</label>
				</view>
				<view class="weui-cell__bd">
					<input @input="getProvince" class="weui-input" placeholder="本省新增感染人数" />
				</view>
			</view>
			<!-- 全国新增 -->
			<view class="weui-cell weui-cell_active">
				<view class="weui-label">全国新增：
					<label style="color: red;">*</label>
				</view>
				<view class="weui-cell__bd">
					<input @input="getCountry" class="weui-input" placeholder="全国新增感染人数" />
				</view>
			</view>
			<!-- 上传通知封面 -->
			<view class="weui-cells">
			            <view class="weui-cell weui-cell_uploader">
			                <view class="weui-cell__bd">
			                    <view class="weui-uploader">
			                        <view class="weui-uploader__hd">
			                          <view aria-role="option" class="weui-uploader__overview">
			                              <view class="weui-uploader__title">图片上传：
											<label style="color: red;">*</label>
										  </view>
			                              <view class="weui-uploader__info">{{files.length}}/1</view>
			                          </view>
			                          <view class="weui-uploader__tips">
			                            请上传通知封面
			                          </view>
			                        </view>
			                        <view class="weui-uploader__bd">
			                            <view class="weui-uploader__files" id="uploaderFiles">
											<block v-for="(item, index) in files" :key="index">
												<view class="weui-uploader__file" @click="previewImage" :id="item" @longpress="deleted(index)">
													<image class="weui-uploader__img" :src="item" mode="aspectFill" />
												</view>
											</block>
										</view>
			                            <view class="weui-uploader__input-box">
			                                <view aria-role="button" aria-label="上传" class="weui-uploader__input" @click="chooseImage"></view>
			                            </view>
			                        </view>
			                    </view>
			                </view>
			            </view>
			        </view>
		</view>
			<!-- 填写通知区域 -->
			<view class="weui-cells__title">具体通知内容：
				<label style="color: red;">*</label>
			</view>
			<view class="weui-cells weui-cells_after-title">
				<view class="weui-cell weui-cell_active">
					<view class="weui-cell__bd">
						<textarea maxlength="-1" @input="getInfo" class="weui-textarea" placeholder-class="weui-input_placeholder" placeholder="请输入通知内容" ></textarea>
						<view aria-role="option" title="字数统计" class="weui-textarea-counter">0/200</view>
					</view>
				</view>
			</view>
			<!-- 按钮 -->
			<view class="button-sp-area" style="margin-top: 50rpx; margin-bottom: 100rpx; width: 100%;">
				<navigator class="button weui-btn weui-btn_mini weui-btn_primary weui-wa-hotarea" aria-role="button" @click="submit" style="width: 100%;">提交通知</navigator>
			</view> 
	</view>
</template>

<script>
	import helper from '@/base.js';  
	export default {
		name:"countryInfo",
		data() {
			return {
				files: [],
				host:helper.host,
				title:'',
				localIncrease:'-1',
				provinceIncrease:'-1',
				countryIncrease:'-1',
				images:'',
				noticeInfo:'',
				submitTime:'',
				submitUser:'',
				submitUserId:'',
				noticeType:'1',
				index:0,
				i:'data:image/png;base64,'
			};
		},
		onLoad() {
			
		},
		methods:{
			deleted:function(e){
				var that = this;
				var images = that.files;
				var index = e;//获取当前长按图片下标
				wx.showModal({
				  title: '提示',
				  content: '确定要删除此图片吗？',
				  success: function (res) {
					if (res.confirm) {
					  console.log('点击确定了');
					  images.splice(index, 1);//通过splice方法删除splice(index,1),删除一个当前元素
					  console.log(images)
					  that.files=images
					  that.index=that.index-1
					that.files = that.files.filter(function (s) {
						  return s && s.trim(); 
						});
					
					} else if (res.cancel) {
					  console.log('点击取消了');
					  return false;
					}
				   
				  }
				})
			
			},
			  chooseImage() {
			    const that = this;
			    wx.chooseImage({
			      sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
			      sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
			      success(res) {
					  console.log(res)
			        // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
			        that.files[that.index]=res.tempFilePaths[0]
					that.index = that.index+1
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
						}
					})
			      },
			    });
			  },
			  previewImage(e) {
			    wx.previewImage({
			      current: e.currentTarget.id, // 当前显示图片的http链接
			      urls: this.files, // 需要预览的图片http链接列表
			    });
			  },
			  
			  getTitle:function(e){
				  this.title=e.detail.value
			  },
			  getLocal:function(e){
				  this.localIncrease=e.detail.value
			  },
			  getProvince:function(e){
				  this.provinceIncrease=e.detail.value
			  },
			  getCountry:function(e){
				  this.countryIncrease=e.detail.value
			  },
			  getInfo:function(e){
				  this.noticeInfo=e.detail.value
			  },
			  getNowFormatDate:function() {
			       var date = new Date();
			       var seperator1 = "-";
			       var seperator2 = ":";
			       var month = date.getMonth() + 1;
			       var strDate = date.getDate();
			       if (month >= 1 && month <= 9) {
			           month = "0" + month;
			       }
			       if (strDate >= 0 && strDate <= 9) {
			           strDate = "0" + strDate;
			       }
			       var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate
			  
			       return currentdate; 
			  },
			  submit:function(e){
				  var images=""
				  for(var i =0; i<this.files.length;i++){
					  if(i!=this.files.length-2){
						  images=images+this.files[i]+"&&"
					  }else{
						  images=images+this.files[i]
					  }
				  }

				  
				  this.submitTime=this.getNowFormatDate()
				  uni.request({
				  	url:this.host+'api/notice/addNotice',
					method:'POST',
					data:{
						"title":this.title,
						'localIncrease':this.localIncrease,
						'provinceIncrease':this.provinceIncrease,
						'countryIncrease':this.countryIncrease,
						"images":this.i,
						"noticeType":'1',
						'noticeInfo':this.noticeInfo,
						'submitTime':this.submitTime,
						'submitUser':uni.getStorageSync('username'),
						'submitUserId':uni.getStorageSync('userId')
					},
					header:{
						"token":uni.getStorageSync('token')
					},
					success: (res) => {
						console.log(res)
						if(res.data.code==200){
							uni.showToast({
								title:'发布成功',
								duration:1000
							})
						}else{
							uni.showToast({
								title:'发布失败',
								duration:1000
							})
						}
					},
					fail: (res) => {
						console.log(res)
						uni.showToast({
							title:'发布失败',
							duration:1000
						})
					}
				  })
			  }
		}
	}
</script>

<style lang="scss">
.button{
	padding: 15rpx;
}
</style>
