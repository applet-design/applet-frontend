<template>
	<view class="root">
		<view class="topTab">
			<view @click="changeTab(index)" :class="chooseTabIndex==index?'show':''" class="tabItem"  v-for="(item,index) in tabTitle">
				{{item}}
			</view>
		</view>
		<!-- 页面图表 -->
		<view class="chart">
			<view>
				<view class="container" :class="show?'shows':'noShows'" >
				    <canvas canvas-id="pieCanvas" class="canvas" style="height:350px;width: 400px;" bindtouchstart="touchHandler"></canvas>
					 <canvas canvas-id="columnCanvas" class="canvas" style="height:200px;width: 400px;" bindtouchstart="touchHandler"></canvas>
				</view>
				<view :class="show?'noShows':'shows'">
					<qiun-data-charts
					type="word"
					:opts="opts"
					:chartData="data"
				  />
				  <view>
					   <canvas canvas-id="columnCanvass" class="canvas" style="height:200px;width: 400px; display: block;" bindtouchstart="touchHandler"></canvas>
				  </view>
				  
				</view>
				
			</view>
		</view>
	</view>
</template>
<script>
	import WordCloud from '../../wordcloud2.js'
	var wxCharts=require('../../wxcharts.js')
	import helper from '@/base.js';  
	export default {
		data() {
			return {
				index:'',
				name:'',
				tabTitle:'',
				chooseTabIndex:'0',
				chooseTabInfo:'',
				showData:'',
				host:helper.host,
				datatype:'',
				data:'',
				canvas:'',
				wordData:'',
				show:'',
				opts: {
				        color: ["#1890FF","#91CB74","#FAC858","#EE6666","#73C0DE","#3CA272","#FC8452","#9A60B4","#ea7ccc"],
				        padding: undefined,
				        extra: {
				          word: {
				            type: "normal",
				            autoColors: false
				          }
				        }
				      },
				categories:[''],
				categoriesData:[''],
				names:'',
				max:0
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
			// 定义表头
			if(name=='外出申请表单'){
				this.tabTitle=['近期外出地点分析','近期外出事件分析']
				this.chooseTabInfo='近期外出地点分析'
			}else if(name=='健康信息表单'){
				this.tabTitle=['体温比例','未填写报表比例']
				this.chooseTabInfo='体温比例'
			}else if(name=='物资申请表'){
				this.tabTitle=['申请物资分析','剩余物资分析']
				this.chooseTabInfo='申请物资分析'
			}else if(name=='外来报备表'){
				this.tabTitle=['近期返回家庭分析','近期返回地点分析']
				this.chooseTabInfo='近期返回家庭分析'
			}else if(name=='词云分析'){
				this.tabTitle=['讨论区词云分析','讨论区热帖分析']
				this.chooseTabInfo='讨论区词云分析'
			}
			
			
		var api=""
		if(this.chooseTabInfo=='近期外出地点分析'){
			api='api/analysis/destination'
			this.names='外出地点'
		}else if(this.chooseTabInfo=='近期外出事件分析'){
			api='api/analysis/reason'
			this.names='外出事件'
		}else if(this.chooseTabInfo=='体温比例'){
			api='api/analysis/tem'
			this.names='体温'
		}else if(this.chooseTabInfo=='未填写报表比例'){
			api='api/analysis/tem'  //未做
			this.names='填写人数'
		}else if(this.chooseTabInfo=='申请物资分析'){
			api='api/analysis/materialName'
			this.names='申请物资'
		}else if(this.chooseTabInfo=='剩余物资分析'){
			api='api/analysis/ableMaterial'
			this.names='剩余物资'
		}else if(this.chooseTabInfo=='近期返回家庭分析'){
			api='api/analysis/goBack'
			 this.names='返家住户'
		}else if(this.chooseTabInfo=='近期返回地点分析'){
			api='api/analysis/gohome'
			this.names='返家居民'
		}else if(this.chooseTabInfo=='讨论区词云分析'){
			api='api/notice/getCloud'
			 this.names='词云分析'
		}else if(this.chooseTabInfo=='讨论区热帖分析'){
			api='api/analysis/reason'// 未做
		}
		uni.request({
			url:this.host+api,
			data:{
				
			},
			headers:{
				
			},
			success:(res)=>{
				this.data=res.data.data
				var windowWidth = 320;
				if(this.chooseTabInfo=='讨论区词云分析'){
					let rs={
						series:res.data.data
					}
					this.data = JSON.parse(JSON.stringify(rs));
					this.show=false
					
					for(var i =0;i<this.data.series.length;i++){
						this.categories[i]=this.data.series[i].name
						this.categoriesData[i]=this.data.series[i].textSize
						if(this.data.series[i].textSize>this.max){
							this.max=this.data.series[i].textSize
						}
					}
					
					
					var columnChart = new wxCharts({
						canvasId: 'columnCanvass',
						type: 'column',
						animation: true,
						categories: this.categories,
						series: [{
							name: this.names,
							data: this.categoriesData,
							format: function (val, name) {
								return val;
							}
						}],
						yAxis: {
							format: function (val) {
								return val ;
							},
							// title: 'hello',
							min: 0
						},
						xAxis: {
							disableGrid: false,
							type: 'calibration'
						},
						extra: {
							column: {
								width: 15
							}
						},
						enableScroll: true,
						// enableScroll:true,是否滑动配置
						width: wx.getSystemInfoSync().windowWidth,
						height: 200,
					});
					
				}else if(this.chooseTabInfo=='讨论区热帖分析'){
					uni.showToast({
						title:'待开发',
						duration:1000,
						icon:'error'
					})
					this.categories=[]
					this.categoriesData=[]
				}else{
					this.show=true
					var pieChart = new wxCharts({
						animation: true,
						canvasId: 'pieCanvas',
						type: 'pie',
						series: this.data,
						width: windowWidth,
						height: 300,
						dataLabel: true,
					});

					
					for(var i =0;i<this.data.length;i++){
						this.categories[i]=this.data[i].name
						this.categoriesData[i]=this.data[i].data
						if(this.data[i].data>this.max){
							this.max=this.data[i].data
						}
					}
					
					
					var columnChart = new wxCharts({
						canvasId: 'columnCanvas',
						type: 'column',
						animation: true,
						categories: this.categories,
						series: [{
							name: this.names,
							data: this.categoriesData,
							format: function (val, name) {
								return val.toFixed(0);
							}
						}],
						yAxis: {
							format: function (val) {
								return val ;
							},
							// title: 'hello',
							min: 0
						},
						xAxis: {
							disableGrid: false,
							type: 'calibration'
						},
						extra: {
							column: {
								width: 15
							}
						},
						enableScroll: true,
						// enableScroll:true,是否滑动配置
						width: wx.getSystemInfoSync().windowWidth,
						height: 200,
					});
				}
				
			}
		})
		
		},
		methods:{
			
			
			       
		
			
			changeTab:function(e){
				this.chooseTabIndex=e
				this.chooseTabInfo=this.tabTitle[e]
				this.datatype=this.getDataType(this.chooseTabInfo)
				console.log('获取数据类型为：'+this.datatype)
				var api=""
				if(this.chooseTabInfo=='近期外出地点分析'){
					api='api/analysis/destination'
					this.names='外出地点'
				}else if(this.chooseTabInfo=='近期外出事件分析'){
					api='api/analysis/reason'
					this.names='外出事件'
				}else if(this.chooseTabInfo=='体温比例'){
					api='api/analysis/tem'
					this.names='体温'
				}else if(this.chooseTabInfo=='未填写报表比例'){
					api='api/analysis/tem'  //未做
					this.names='填写人数'
				}else if(this.chooseTabInfo=='申请物资分析'){
					api='api/analysis/materialName'
					this.names='申请物资'
				}else if(this.chooseTabInfo=='剩余物资分析'){
					api='api/analysis/ableMaterial'
					this.names='剩余物资'
				}else if(this.chooseTabInfo=='近期返回家庭分析'){
					api='api/analysis/goBack'
					 this.names='返家住户'
				}else if(this.chooseTabInfo=='近期返回地点分析'){
					api='api/analysis/gohome'
					this.names='返家居民'
				}else if(this.chooseTabInfo=='讨论区词云分析'){
					api='api/notice/getCloud'
					 this.names='词云分析'
				}else if(this.chooseTabInfo=='讨论区热帖分析'){
					api='api/analysis/reason'
				}
				uni.request({
					url:this.host+api,
					data:{
						
					},
					headers:{
						
					},
					success:(res)=>{
						this.data=res.data.data
						var windowWidth = 320;
						if(this.chooseTabInfo=='讨论区词云分析'){
							let rs={
								series:res.data.data
							}
							 this.data = JSON.parse(JSON.stringify(rs));
							this.show=false
							
							for(var i =0;i<this.data.series.length;i++){
								this.categories[i]=this.data.series[i].name
								this.categoriesData[i]=this.data.series[i].textSize
								if(this.data.series[i].textSize>this.max){
									this.max=this.data.series[i].textSize
								}
							}
							
							
							var columnChart = new wxCharts({
								canvasId: 'columnCanvass',
								type: 'column',
								animation: true,
								categories: this.categories,
								series: [{
									name: this.names,
									data: this.categoriesData,
									format: function (val, name) {
										return val;
									}
								}],
								yAxis: {
									format: function (val) {
										return val ;
									},
									// title: 'hello',
									min: 0
								},
								xAxis: {
									disableGrid: false,
									type: 'calibration'
								},
								extra: {
									column: {
										width: 15
									}
								},
								enableScroll: true,
								// enableScroll:true,是否滑动配置
								width: wx.getSystemInfoSync().windowWidth,
								height: 200,
							});
							
						}else if(this.chooseTabInfo=='讨论区热帖分析'){
						uni.showToast({
							title:'待开发',
							duration:1000,
							icon:'error'
						})
						this.categories=[]
						this.categoriesData=[]
						}else{
							this.show=true
							var pieChart = new wxCharts({
								animation: true,
								canvasId: 'pieCanvas',
								type: 'pie',
								series: this.data,
								width: windowWidth,
								height: 300,
								dataLabel: true,
							});
							
							
							for(var i =0;i<this.data.length;i++){
								this.categories[i]=this.data[i].name
								this.categoriesData[i]=this.data[i].data
								if(this.data[i].data>this.max){
									this.max=this.data[i].data
								}
							}
							
							
							var columnChart = new wxCharts({
								canvasId: 'columnCanvas',
								type: 'column',
								animation: true,
								categories: this.categories,
								series: [{
									name: this.names,
									data: this.categoriesData,
									format: function (val, name) {
										return val.toFixed(0);
									}
								}],
								yAxis: {
									format: function (val) {
										return val ;
									},
									// title: 'hello',
									min: 0
								},
								xAxis: {
									disableGrid: false,
									type: 'calibration'
								},
								extra: {
									column: {
										width: 15
									}
								},
								enableScroll: true,
								// enableScroll:true,是否滑动配置
								width: wx.getSystemInfoSync().windowWidth,
								height: 200,
							});
						}
						
					}
				})
			},
			getDataType:function(str){
				console.log(str)
				if(str=='近期外出地点分析'){
					return 0
				}else if(str=='近期外出事件分析'){
					return 1
				}else if(str=='体温比例'){
					return 2
				}else if(str=='未填写报表比例'){
					return 3
				}else if(str=='申请物资分析'){
					return 4
				}else if(str=='剩余物资分析'){
					return 5
				}else if(str=='近期返回家庭分析'){
					return 6
				}else if(str=='近期返回地点分析'){
					return 7
				}else if(str=='讨论区词云分析'){
					return 8
				}else if(str=='讨论区热帖分析'){
					return 9
				}
			},
			getData:function(){
				
			}
			
		},
		onReady() {
			this.chooseTabInfo=this.tabTitle[this.chooseTabIndex]
			// 获取需要显示的数据信息
			this.datatype=this.getDataType(this.chooseTabInfo)
			console.log('获取数据类型为：'+this.datatype)
			this.getData()
		}
	}
</script>

<style lang="scss">
	.shows{
		display: block;
	}
	.noShows{
		display: none;
	}
 .charts-box {
    width: 100%;
    height: 300px;
  }
page{
	background-color: #FFFFFF;
	width: 100%;
	height: 100%;
}

.root{
	width: 100%;
	height:100%;
}
.topTab{
	width: 100%;
	height: 100rpx;
	display: flex;
	box-shadow: 0 0 10rpx #CCCCCC;
}
.tabItem{
	display: inline-block;
	width: 49%;
	text-align: center;
	line-height: 100rpx;
	font-family: '宋体';
	font-size: 28rpx;
}
.show{
	border-bottom: 1rpx solid #FF0000;
	font-size: 32rpx;
	color: #FF0000;
	font-weight: bold;
}


</style>
