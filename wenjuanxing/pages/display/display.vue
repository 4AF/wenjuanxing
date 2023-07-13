<template>
	<view class="display">
		<view class="title">
			<uni-title type="h1" :title="title" align="center"></uni-title>
			<view class="introduce"> {{introduce}} </view>
		</view>
		
		<view class="horizontal-line"></view>
		<uni-list class="content" v-for="(item,index) in dataArr" :index="index" :key="index">
			<uni-card v-if="item.type==='gap'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<uni-easyinput></uni-easyinput>
				</uni-section>
			</uni-card>
			<uni-card v-else-if="item.type==='single'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<view class="uni-px-5 uni-pb-5">
						<uni-data-checkbox  :localdata="item.choices"></uni-data-checkbox>
					</view>
				</uni-section>
			</uni-card>
			<uni-card v-else-if="item.type==='multi'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<view class="uni-px-5 uni-pb-5">
						<uni-data-checkbox multiple 
							:localdata="item.choices"></uni-data-checkbox>
					</view>
				</uni-section>
			</uni-card>
		</uni-list>
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				dataArr: [],
				title: '',
				introduce: ''
			}
		},
		onLoad() {
			this.title = buffer.questionnaireTitle.title;
			this.introduce = buffer.questionnaireTitle.introduce;
			this.dataArr = buffer.questionnaire;
			console.log(this.dataArr)
		},
		methods: {
			change(e) {
				console.log('e:', e);
			}
		}
	}
</script>

<style lang="scss">
	.display {
		display: flex;
		flex-direction: column;
	}

	.title {
		padding: 20px 20px 0 20px;
		.introduce{
			font-size: 16px;
			color: #a1a1a1;
		}
	}

	.horizontal-line {
		border-top: 1px solid #dcdcdc;
		/* 设置上边框为实线，颜色为黑色 */
		margin: 20px 0;
		/* 设置上下间距，根据需要调整 */
	}
</style>