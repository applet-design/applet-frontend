<template>
	<view class="smallTable">
		<view>
			<view style="display: flex;">
				<view class="samllItem">
					<label class='labels'>
						{{reciveUserItem[0]}}：
					</label>
					{{userData[reciveUserItemIndex[0]]}}</view>
				<view class="samllItem" style="100rpx">
					<label class='labels'>
						{{reciveUserItem[1]}}：
					</label>
					{{userData[reciveUserItemIndex[1]]}}</view>
			</view>
			<view class="samllItem">
				<label class='labels'>
					{{reciveUserItem[2]}}：
				</label>
				{{userData[reciveUserItemIndex[2]]}}</view>
			<view class="samllItem">
				<label class='labels'>
					{{reciveUserItem[3]}}：
				</label>
				{{ (userData[reciveUserItemIndex[3]]==0 || userData[reciveUserItemIndex[3]]==1)? (userData[reciveUserItemIndex[3]] ==0?'不紧急':'紧急'):userData[reciveUserItemIndex[3]]}}</view>
			<!-- 功能栏 -->
			<view class="funTab">
				<view class="time" style=" overflow: hidden; text-overflow:ellipsis; ">
					<label class='labels'>
						<!-- 提交时间： -->
						{{reciveUserItem[4]}}：
					</label>
					<!-- 2022-5-21 -->
					{{userData[reciveUserItemIndex[4]]}}
				</view>
				<view class="likeButton first" @click="backApplication">
					查看
				</view>
				<view class="likeButton " @click="lookDetails">
					审批
				</view>
				
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:'husmalltable',
		props:["reciveUserItemIndex",'reciveUserItem','userData'],
		data() {
			return {
				tabSelect:['通过','为通过'],
				tabBarName:'',
				applicantId:'',
				host:'http://127.0.0.1:8080/',
			};
		},
		onReady() {

		},
		onLoad() {
			
		},
		methods:{
			backApplication:function(e){
				console.log(this.$props.userData[0])
				this.$emit("backApplication",this.$props.userData['applicantId']);	
			},
			lookDetails:function(e){
				console.log(this.$props.userData[0])
				this.$emit("lookDetails",this.$props.userData['applicantId']);	

			}
		}
	}
</script>

<style lang="scss">
.smallTable{
	padding-left: 30rpx;
	padding-top: 40rpx;
	width: 100%;
	height: 300rpx;
	box-shadow: 0 0 5px #CCCCCC;
	position: relative;
}
.funTab{
	width: 100%;
	height: 100rpx;
	bottom: 0rpx;
	display: flex;
	text-align: center;
	margin-left: 20rpx;
	margin-top: 10rpx;
}

.likeButton{
	box-shadow: 0 0 5px #CCCCCC;
	margin-left: 10rpx;
	width: 150rpx;
	height: 75rpx;
	text-align: center;
	line-height: 75rpx;
}
.first{
	margin-left: 30px;
}
.samllItem{
	display: flex;
	margin-left: 20rpx;
	margin-top: 10rpx;
	width: 60%;
	font-size: 28rpx;
	font-family: '微软雅黑';
}
.labels{
	font-weight: bold;
}
</style>
