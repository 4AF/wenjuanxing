<template>
	<view class="container">
		<view class="logo">
			<image src="/static/logo-all.png" class="logo-image" mode="aspectFit" />
		</view>
		<view class="form">
			<view class="form-item">
				<input v-model="userId" type="text" placeholder="五位用户id" class="input" @blur="testId"/>
			</view>
			<view class="form-item">
				<input v-model="username" type="text" placeholder="用户名" class="input" @blur="testName"/>
			</view>
			<view class="form-item">
				<input v-model="password" type="password" placeholder="密码" class="input" @blur="testPassword"/>
			</view>
			<view class="form-item">
				<input v-model="confirmPassword" type="password" placeholder="确认密码" class="input" @blur="testPassword2"/>
			</view>
			<view class="form-item">
				<button class="btn" @tap="register">注册</button>
			</view>
		</view>
	</view>
</template>

<script>
import buffer from '../../common/buffer';
	export default {
		data() {
			return {
				userId:'',
				username: '',
				password: '',
				confirmPassword: ''
			};
		},
		methods: {
			testId(){
				var regex=/^[1-9][0-9]{4}$/;
				if(!regex.test(this.userId)){
					uni.showToast({
						title:'id不规范',
						icon:'none'
					})
					return
				}
			},
			testName(){
				if(this.username===''){
					uni.showToast({
						title:'用户名不能为空！',
						icon:'none'
					})
					return
				}
			},
			testPassword(){
				var regex2=/^[a-zA-Z0-9]{6,}$/;
				if(!regex2.test(this.password)){
					uni.showToast({
						title:'密码不规范！',
						icon:'none'
					})
					return
				}
			},
			testPassword2(){
				if(this.password!==this.confirmPassword){
					uni.showToast({
						title:'密码不一致',
						icon:'none'
					})
					return
				}
			},
			register() {
				// 注册逻辑
				// 可以在这里调用后端接口进行用户注册
				// 处理注册成功或失败的情况
				var regex=/^[1-9][0-9]{4}$/;
				if(!regex.test(this.userId)){
					uni.showToast({
						title:'id不规范',
						icon:'none'
					})
					return
				}
				if(this.username===''){
					uni.showToast({
						title:'用户名不能为空！',
						icon:'none'
					})
					return
				}
				var regex2=/^[a-zA-Z0-9]{6,}$/;
				if(!regex2.test(this.password)){
					uni.showToast({
						title:'密码不规范！',
						icon:'none'
					})
					return
				}
				if(this.password!==this.confirmPassword){
					uni.showToast({
						title:'密码不一致',
						icon:'none'
					})
					return
				}
				uni.request({
					url:buffer.serverIp+'/user/register',
					method:'POST',
					data:{
						user_id:parseInt(this.userId),
						username:this.username,
						password:this.password
					},
					success: (res) => {
						console.log(res.data)
						if(res.data.status===500&&res.data.data===undefined){
							uni.showToast({
								title:'连接有问题！',
								icon:'none'
							})
							return
						}
						else if(res.data.status===500){
							uni.showToast({
								title:res.data.msg,
								icon:'none'
							})
						}
						else if(res.data.status===200){
							uni.showToast({
								title:'注册成功，即将跳转到登录界面',
								icon:'none'
							})
							setTimeout(() => {
							  // 在这里编写需要延迟执行的代码
							  uni.reLaunch({
							  	url: '/pages/login/login'
							  })
							}, 500);
						}
					}
				})
			},
		},
	};
</script>

<style>
	.container {
		background-color: #f0f0f0;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100vh;
		padding: 15rpx;
	}
	
	.logo {
		position: relative;
		margin-bottom: 20px;
	}
	
	.logo-image {
		width: 100px;
		height: 100px;
	}
	
	.form {
		margin-top: 10px;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 300px;
	}
	
	.form-item {
		margin-bottom: 20px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	
	.input {
		width: 200px;
		padding: 10px;
		border: 1px solid #ccc;
		border-radius: 4px;
	}
	
	.btn {
		width: 150px;
		height: 50px;
		background: #0066cc;  
		color: #fff;
		font-size: 20px;
		border: none;
		border-radius: 4px;
		cursor: pointer;
	}
</style>