<template>
	<view class="add">
		<form @submit="onSubmit">
			<view class="item">
				<input v-model="formValue.title" type="text" name="title" placeholder="请输入标题"></input>
			</view>
			
			<view class="item">
				<input v-model="formValue.author"  type="text" name="author" placeholder="请输入作者"></input>
			</view>
			
			<view class="item">
				<textarea v-model="formValue.content"  name="content" placeholder="请输入详细内容"></textarea>
			</view>
			
			<view class="item">
				<uni-file-picker
					v-model="imageValue" 
					fileMediatype="image" 
					mode="grid"   
					@success="uploadSuccess" 
				/>
			</view>
			
			<view class="item">
				<button form-type="reset">重置</button>
				<button form-type="submit" type="primary" :disabled="isDisable(formValue)">提交</button>
			</view>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formValue:{
					title:"",
					author:"",
					content:""
				},
				imageValue:[],
				picurls:[],
			};
		},
		methods:{
			
			//图片上传成功
			uploadSuccess(e){
				console.log(e)
				this.picurls = e.tempFilePaths
			},
			
			//判断按钮是否禁用
			isDisable(obj){
				let bool = Object.keys(obj).some((key,value)=>{
					return obj[key] == ''
				})
				return bool;
			},
			
			//点击提交
			onSubmit(e){
				let detail = e.detail.value
				uniCloud.callFunction({
					name:"art_add_row",
					data:{
						detail,
						picurls:this.picurls
					}
				}).then(res => {
					
					uni.showToast({
						title:"发布成功!"
					})
					
					setTimeout(()=>{
						uni.reLaunch({
							url:"/pages/index/index"
						})
					},1500)
					
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.add{
		padding: 30rpx;
		.item{
			padding-bottom: 20rpx;
			input,textarea{
				border: 1rpx solid #eee;
				height: 80rpx;
				padding: 0 20rpx;
			}
			textarea{
				height: 300rpx;
				width: 100%;
				box-sizing: border-box;
			}
			button{
				margin-top: 20rpx;
			}
		}	
	}



</style>
