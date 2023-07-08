<template>
	<view class="home">
		<view class="content">
			<view @click="goDetail(item._id)" class="item" v-for="item in listArr" :key="item._id">
				<view class="view">
					<view class="title">{{item.title}}</view>
					<view class="info">
						<view>{{item.author}}</view>
						<view>
							<uni-dateformat :date="item.posttime" :threshold="[60000,7200000]" format="MM-dd"></uni-dateformat>
						</view>
						<!-- <view>{{item.content}}</view> -->
						<view>删除</view>
					</view>
				</view>
				<view class="pic">
					<image v-if="item.picurls && item.picurls.length" mode="aspectFill" :src="item.picurls[0]" alt="">
					<image v-else mode="aspectFill" src="/static/images/nopic.jpg" alt="">
				</view>
			</view>
		</view>
		
		<view class="goAdd" @click="goAdd">+</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr:[]
			}
		},
		onLoad() {
			this.getData();
		},
		//触底方法
		onReachBottom() {
			this.getData();
		},
		//下拉刷新
		onPullDownRefresh() {
			this.listArr = []
			this.getData();
		},
		methods: {
			//跳转到详情页面
			goDetail(e){
				uni.navigateTo({
					url:"/pages/detail/detail?id="+e
				})
			},
			//获取数据库的列表
			getData(){
				uniCloud.callFunction({
					name:"art_get_all",
					data:{
						skip:this.listArr.length 
					}
				}).then(res=>{
					console.log(res);
					let oldList = this.listArr
					let newList = [...oldList,...res.result.data]
					this.listArr = newList
					uni.stopPullDownRefresh()
				})
			},
			
			//点击跳转到编辑页面去
			goAdd(){
				uni.navigateTo({
					url:"/pages/add/add"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	:root{
		box-sizing: border-box;
	}
	
	.home{
		.content{
			padding: 30rpx;
			.item{
				display: flex;
				justify-content: space-between;
				padding: 20rpx;
				border-bottom: 1rpx solid #eee;
				.view{
					flex: 1;
					display: flex;
					flex-direction: column;
					justify-content: space-between;
					padding-right: 20rpx ;
					.title{
						font-size: 40rpx;
						color: #333;
						view-align: justify;
						view-overflow: -o-ellipsis-lastline;
						overflow: hidden;				//溢出内容隐藏
						view-overflow: ellipsis;		//文本溢出部分用省略号表示
						display: -webkit-box;			//特变显示模式
						-webkit-line-clamp: 2;			//行数
						line-clamp: 2;
						-webkit-box-orient: vertical; 	//盒子中内容竖直排列
					}
					.info{
						 font-size: 28rpx;
						 color: #888; 
						 // 设置宽度/最大宽度
						 width: 100%; 
						 // 核心代码
						 overflow: hidden; //超出文本隐藏
						 view-overflow: ellipsis; ///超出部分省略号显示
						 display: -webkit-box; //弹性盒模型
						 -webkit-box-orient: vertical; //上下垂直
						 -webkit-line-clamp: 2; //自定义行数
						 view{
							 margin-right: 20rpx;
						 }
					}
				}
				.pic{
					width: 260rpx;
					height: 180rpx;
					image{
						width: 100%;
						height: 100%;
					}
				}
			}
		}
	
		.goAdd{
			width: 120rpx;
			height: 120rpx;
			background: #2B9939;
			color: #fff;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 50%;
			font-size: 50rpx;
			position: fixed;
			right: 60rpx;
			bottom: 100rpx;
			box-shadow: 0 0 20rpx rgba(43, 153, 57, 0.1);
			
		}
	
	}
</style>
