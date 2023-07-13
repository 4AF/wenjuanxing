<template>
	<view class="Create">
		<view class="horizontal-line"></view>
		<uni-forms :modelValue="formData" label-position="top">
			<uni-forms-item label="调查名称" name="name" required="true">
				<uni-easyinput type="text" v-model="formData.name" placeholder="请输入调查名称" />
			</uni-forms-item>
			<uni-forms-item label="调查简介" name="introduce">
				<uni-easyinput type="textarea" v-model="formData.introduce" placeholder="请输入调查简介" />
			</uni-forms-item>
		</uni-forms>
		<button @tap="submitForm">创建问卷</button>
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				// 表单数据
				formData: {
					name: '',
					introduce:''
				}
			}
		},
		onShow() {
			buffer.questionnaire=[];
			buffer.questionnaireTitle={
				title:'',
				introduce:''
			};
		},
		methods: {
			/**
			 * 复写 binddata 方法，如果只是为了校验，无复杂自定义操作，可忽略此方法
			 * @param {String} name 字段名称
			 * @param {String} value 表单域的值
			 */
			// binddata(name,value){
			// 通过 input 事件设置表单指定 name 的值
			//   this.$refs.form.setValue(name, value)
			// },
			// 触发提交表单
			submitForm() {
				if(this.formData.name===''){
					uni.showToast({
						title:"问卷名字必须填写！！！",
						icon:'none'
					});
					return;
				}
				uni.navigateTo({
					url: '/pages/Questionnaire/Questionnaire?name='+this.formData.name+'&introduce='+this.formData.introduce
				})
			}
		}
	}
</script>

<style scoped>
	.Create {
		/* margin-top: 20px; */
		padding: 15px;
		background-color: #fff;
	}

	.horizontal-line {
		border-top: 1px solid #dcdcdc;
		/* 设置上边框为实线，颜色为黑色 */
		margin:20px 0;
		/* 设置上下间距，根据需要调整 */
	}

	button {
		background-color: #00aaff;
		color: #fff;
	}
</style>