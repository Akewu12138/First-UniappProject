<template>
	<view class="edit">
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
	let id;
	export default {
		data() {
			return {
				formValue:{
					title:"",
					author:"",
					content:""
				},
				imageValue:[],
				picurls:[]
			};
		},
		onLoad(e) {
			id = e.id
			this.getDetail()
		},
		methods:{
			//图片上传成功
			uploadSuccess(e){
				this.picurls = e.tempFilePaths
			},
			//获取详情
			getDetail(){
				uniCloud.callFunction({
					name:"art_get_row",
					data:{
						id
					}
				}).then(res => {
					console.log(res.result.data[0]);
					this.formValue = res.result.data[0];
					if(!this.formValue.picurls) return;
					let urls = this.formValue.picurls.map(item=>{
						return {url:item}
					})
					this.imageValue = urls
				})
			},	
			
			//判断按钮是否禁用
			isDisable(obj){
				let bool = Object.keys(obj).some((key,value)=>{
					return obj[key] == ''
				})
				return bool;
			},
			
			//点击提交
			onSubmit(){
				let _picurls = this.imageValue.map(item=>{
					return item.url;
				})
				uniCloud.callFunction({
					name:"art_update_row",
					data:{
						detail:this.formValue,
						picurls:_picurls
					}
				}).then(res => {
					uni.showToast({
						title:"修改成功"
					})
					setTimeout(()=>{
						uni.navigateBack()
					},1000)
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.edit{
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
