<template>
	<view class="analysis">
		<view class="title">
			{{title}}
		</view>
		<view class="introduce">
			{{introduce}}
		</view>
		<view class="horizontal-line"></view>
		<view class="content" v-for="(item,index) in formData" :index="index" :key="index">
			<uni-card v-if="item.type==='gap'" :title="index+1+'.'+item.title">
				<uni-collapse>
					<uni-collapse-item title="点击展开详情" >
						<view class="details">
							<view class="text">
								<uni-table border stripe emptyText="暂无更多数据" >
									<!-- 表头行 -->
									<uni-tr>
										<uni-th align="center">序号</uni-th>
										<uni-th align="center">答案</uni-th>
									</uni-tr>
									<!-- 表格数据行 -->
									<uni-tr v-for="(item,index) in item.answer" :index="index" :key="index">
										<uni-td>{{index+1}}</uni-td>
										<uni-td>{{item}}</uni-td> 
									</uni-tr>
								</uni-table>
							</view> 
						</view>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>
			<uni-card v-else :title="index+1+'.'+item.title">
				<uni-table border stripe emptyText="暂无更多数据" >
					<!-- 表头行 -->
					<uni-tr>
						<uni-th align="center">选项</uni-th>
						<uni-th align="center">人数</uni-th>
					</uni-tr>
					<!-- 表格数据行 -->
					<uni-tr v-for="(item,index) in item.answer" :index="index" :key="index">
						<uni-td>{{item.text}}</uni-td>
						<uni-td>{{item.counts}}</uni-td>
					</uni-tr>
				</uni-table>
			</uni-card>
		</view>
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				title: '',
				introduce: '',
				formData: [],
				// e.g.

				id: -1
			}
		},
		onLoad(e) {
			// console.log(e);
			this.id = e.id;
			this.getAnalysis();
		},
		methods: {
			change(e) {
				console.log(e);
			},
			getAnalysis() {
				uni.showLoading({
					title: "数据加载中",
					mask: true
				})
				uni.request({
					url: buffer.serverIp + '/results/return',
					method: 'POST',
					data: {
						// questionnaire_id: this.id,
						questionnaire_id: this.id
					},
					success: (res) => {
						console.log(res);
						// this.objData=res.data;
						// 这里保存从后端拉取的数据
						this.title=res.data.data.title;
						console.log(this.title);
						this.introduce=res.data.data.description;
						console.log(this.introduce);
						for(let i=0;i<res.data.data.info.length;i++){
							console.log(i);
							console.log(res.data.data.info[i]);
							if(res.data.data.info[i].type==='single'){
								console.log(res.data.data.info[i].answer_opt);
								this.formData.push({
									type:'single',
									title:res.data.data.info[i].question_content,
									answer:res.data.data.info[i].answer_opt
								})
							}
							else if(res.data.data.info[i].type==='multi'){
								this.formData.push({
									type:'multi',
									title:res.data.data.info[i].question_content,
									answer:res.data.data.info[i].answer_opt
								})
							}
							else if(res.data.data.info[i].type==='gap'){
								this.formData.push({
									type:'gap',
									title:res.data.data.info[i].question_content,
									answer:res.data.data.info[i].answer_text
								})
							}
						}
					},
					complete: () => {
						uni.hideLoading()
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.analysis {
		background-color: #f0f0f0;
		min-height: 100vh;

		.title {
			padding-top: 30rpx;
			font-size: 46rpx;
			color: #4a4a4a;
			padding-bottom: 20rpx;
			text-align: center;
			font-weight: bold;
		}

		.introduce{
			color:#8c8c8c;
			padding-left: 30rpx;
			padding-right: 30rpx;
		}
		.horizontal-line {
			border-top: 1px solid #dcdcdc;
			/* 设置上边框为实线，颜色为黑色 */
			margin: 20px 0;
			/* 设置上下间距，根据需要调整 */
		}
		.content {
			font-size: 30rpx;
			color: #666;
		}
	}
	
</style>