<template>
	<view class="singleChoice">
		<view class="title">
			<uni-forms :modelValue="formData" label-position="top">
				<uni-forms-item label="标题" required="true">
					<uni-easyinput type="textarea" v-model="formData.title" placeholder="请输入标题" />
				</uni-forms-item>
			</uni-forms>
		</view>
		<view class="line">
			<p class="Font">&ensp;&ensp;选项</p>
		</view>
		<view class="list">
			<view class="choice" v-for="(item,index) in formData.choices" :index="index" :key="index">
				<view class="list-item">
					<image src="../../static/imgs/jinzhi.png" @tap="cancel(index)"></image>
					<input type="text" placeholder="输入选项" v-model="formData.choices[index].text" />
				</view>
			</view>
			<view class="horizontal-line"></view>

			<view class="list-item" @tap="add">
				<image src="../../static/imgs/tianjia.png"></image>
				<p style="font-size: 20px; color: #1296db;">添加选项</p>
			</view>
		</view>

		<view class="line">
			<p class="Font">&ensp;&ensp;设置</p>
		</view>


		<view class="item">
			<p style="font-size: 18px;">此题目必须回答</p>
			<switch color="#1296db" checked @change="change" />
		</view>

		<button @tap="submit">确定</button>
	</view>
</template>
<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				formData: {
					type: 'multi',
					title: '',
					choices: [{
							text: '',
							value: 'none'
						},
						{
							text: '',
							value: 'none'
						}
					],
					mustans: true,
					checkbox: []
				},
				num: -1
			}
		},
		onLoad(e) {
			// console.log(e.index);
			if (e.index !== undefined) {
				this.formData = buffer.questionnaire[e.index];
				this.num = e.index;
				// console.log("有参的时候")
				return;
			};
			// console.log("无参的时候")
		},
		methods: {
			cancel(e) {
				if (this.formData.choices.length === 2) {
					uni.showToast({
						title: '选项不能少于两个!',
						icon: 'none'
					});
					return;
				} else {
					this.formData.choices.splice(e, 1);
				}
				// console.log(this.formData.choices)
			},
			submit() {
				if (this.formData.title === '') {
					uni.showToast({
						title: '标题不能为空!',
						icon: 'none'
					});
					return;
				}
				for (let i = 0; i < this.formData.choices.length; i++) {
					if (this.formData.choices[i].text === '') {
						uni.showToast({
							title: '选项不能为空!',
							icon: 'none'
						});
						return;
					} else {
						this.formData.choices[i].value = i
					}
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
			add() {
				this.formData.choices.push({
						text: '',
						value: 'none'
					})
					// console.log(this.formData.choices)
			},
			change() {
				this.formData.mustans = !this.formData.mustans
				// console.log(this.formData.required)
			}
		}
	}
</script>
<style scoped>
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

	.list-item {
		padding: 10px;
		display: flex;
	}

	.list-item>image {
		padding-right: 15px;
		height: 30px;
		width: 30px;
	}

	.list-item>input {
		height: 30px;
	}

	.horizontal-line {
		border-top: 1px solid #dcdcdc;
		/* 设置上边框为实线，颜色为黑色 */
		/* margin:20px 0; */
		/* 设置上下间距，根据需要调整 */
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