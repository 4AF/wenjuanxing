<template>
	<view>
		<view class="title">
			<uni-forms :modelValue="formData" label-position="top">
				<uni-forms-item label="标题" required="true">
					<uni-easyinput type="textarea" v-model="formData.title" placeholder="请输入标题" />
				</uni-forms-item>
			</uni-forms>
		</view>
		<view class="line">
			<p class="Font">&ensp;&ensp;设置</p>
		</view>
		<view class="item">
			<p style="font-size: 18px;">此题目必须回答</p>
			<switch color="#1296db" checked @change="change"/>
		</view>

		<button @tap="submit">确定</button>
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				// 表单数据
				formData: {
					type: 'gap',
					title: '',
					mustans: true
				},
				num: -1
			}
		},
		onLoad(e) {
			console.log(e.index);
			if (e.index !== undefined) {
				this.formData = buffer.questionnaire[e.index];
				this.num = e.index;
				console.log("有参的时候")
				return;
			};
			console.log("无参的时候")
		},
		methods: {

			submit() {
				if (this.formData.title === '') {
					uni.showToast({
						title: '标题不能为空!',
						icon: 'none'
					});
					return;
				}
				if (this.num === -1) {
					buffer.questionnaire.push(this.formData);
				}
				uni.navigateBack({
					delta: 1,
					success() {
						uni.redirectTo({
							url: '/pages/Questionnaire/Questionnaire?name=' + buffer.questionnaireTitle
								.title + '&introduce=' + buffer.questionnaireTitle.introduce
						})
					}
				})
			},
			change() {
				this.formData.mustans = !this.formData.mustans
				// console.log(this.formData.required)
			}
		}
	}
</script>

<style>
	.title {
		padding: 15px;
	}

	.line {
		height: 30px;
		background-color: #e1e1e1;

	}

	.Font {
		padding-top: 5px;
		padding-bottom: 5px;
		color: #8b8b8b;
	}

	.item {
		padding: 10px;
		display: flex;
		justify-content: space-between;
	}

	button {
		margin: 15px;
		background-color: #00aaff;
		color: #fff;
	}
</style>