<template>
	<view class="container">
		<view class="logo">
			<image src="/static/logo-all.png" class="logo-image" mode="aspectFit" />
		</view>
		<view class="form">
			<view class="form-item">
				<input v-model="userId" type="text" placeholder="账户" class="input input-name" />
			</view>
			<view class="form-item">
				<input v-model="password" type="password" placeholder="密码" class="input input-psw" />
			</view>
			<view class="form-item">
				<button class="btn" @click="login">登录</button>
				<view class="text">没有账号？<text @tap="register">立即注册</text></view>

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
				password: '',
			};
		},
		methods: {
			login() {
				console.log({
						user_id:parseInt(this.userId),
						password:this.password
					})
				uni.request({
					url:buffer.serverIp+'/user/login',
					method:'POST',
					data:{
						user_id:parseInt(this.userId),
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
							return
						}
						else if(res.data.status===200){
							buffer.userId=this.userId;
							uni.showToast({
								title:'欢迎用户'+res.data.data.username+'登录！',
								icon:'none'
							})
							setTimeout(() => {
								  // 在这里编写需要延迟执行的代码
								  uni.redirectTo({
								  	url: '/pages/index/index'
								  })
								}, 1000);
						}
					},
					fail: (res) => {
						console.log(res.data)
					}
				})
				// 登录逻辑
				// 可以在这里调用后端接口进行登录验证
				// 处理登录成功或失败的情况
			},
			register() {
				uni.navigateTo({
					url: '/pages/register/register'
				})
			}
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
		position: relative;
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

	.text {
		padding-top: 10px;
		font-size: 10px;
		text-decoration: underline;
	}
	.text text{
		color: #0066cc;
	}
</style>