<template>
	<view class="index">
		<!-- <uni-search-bar :radius="100" placeholder="搜索问卷" @confirm="search" v-model="searchValue"
			@input="input"></uni-search-bar> -->
		<view class="content" v-for="(item,index) in listArr" :index="index" :key="index">
			<uni-card :title="item.title" :extra="item.released?'已发布':'未发布'" @tap="toggle(item.questionnaireId,index)">
				<text class="uni-body">{{item.introduce}}</text>
			</uni-card>
		</view>
		<view>
			<!-- 普通弹窗 -->
			<uni-popup ref="popup" background-color="#fff">
				<view class="popup-content">
					<view slot="actions" class="card-parent card-actions">
						<view class="card-actions-item" @click="actionsClick1(selectedId,selectedIndex)">
							<uni-icons custom-prefix="iconfont" type="icon-fabu" size="20" color="#999"></uni-icons>
							<text class="card-actions-item-text">发布/暂停</text>
							<!-- <uni-icons v-if="item.released===true" custom-prefix="iconfont" type="icon-zanting1" size="20" color="#999"></uni-icons>
							<text v-if="item.released===true" class="card-actions-item-text">暂停</text> -->
						</view>
						<view class="card-actions-item" @click="actionsClick2(selectedId)">
							<uni-icons custom-prefix="iconfont" type="icon-shanchu" size="20" color="#999"></uni-icons>
							<text class="card-actions-item-text">删除</text>
						</view>
						<view class="card-actions-item" @click="actionsClick3(selectedId)">
							<uni-icons custom-prefix="iconfont" type="icon-tongjijieguo" size="20"
								color="#999"></uni-icons>
							<text class="card-actions-item-text">统计结果</text>
						</view>
					</view>
				</view>
			</uni-popup>
		</view>
		<uni-fab horizontal="left" vertical="bottom" @fabClick="changePage()"></uni-fab>
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				searchValue: '',
				listArr: [],
				selectedId: -1,
				selectedIndex:-1
			}
		},
		onLoad() {
			this.getData();
		},
		methods: {
			changePage() {
				uni.navigateTo({
					url: '../Create/Create'
				})
			},
			getData() {
				uni.showLoading({
					title: "数据加载中",
					mask: true
				})
				uni.request({
					url: buffer.serverIp + '/questionnaires/logindisplay',
					method: 'POST',
					data: {
						user_id: buffer.userId
					},
					success: res => {
						console.log(res);
						for (let i = 0; i < res.data.data.length; i++) {
							this.listArr.push({
								title: res.data.data[i].title,
								introduce: res.data.data[i].description,
								questionnaireId: res.data.data[i].questionnaire_id,
								released: res.data.data[i].published
							})
						};
						buffer.questionnaireList = this.listArr;
						console.log(buffer.questionnaireList);
						console.log(this.listArr)
					},
					complete: () => {
						uni.hideLoading()
					},
					fail: (res) => {
						console.log(res)
					}
				})
			},
			intoAnalysis(e) {
				uni.navigateTo({
					url: '/pages/analysis/analysis?id=' + e
				})
			},
			toggle(id,index) {
				console.log(index);
				this.selectedId = id;
				this.selectedIndex=index;
				console.log(this.selectedIndex);
				// open 方法传入参数 等同在 uni-popup 组件上绑定 type属性
				this.$refs.popup.open('bottom')
			},
			actionsClick1(id,index) {
				console.log(id);
				console.log(index);
				console.log(this.listArr[index].released);
				if (!this.listArr[index].released) {
					console.log("this.listArr[index].released");
					uni.request({
						url: buffer.serverIp + '/questionnaires/publish',
						method: 'POST',
						data: {
							questionnaire_id: id
						},
						success: (res) => {
							console.log(res.data);
							if(res.data.status===200){
								uni.setClipboardData({
									data: 'http://www.wenjuanxing.one/#/?id=' + id
								})
								uni.showToast({
									title: "发布成功！！",
									icon: 'none'
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
							console.log(res)
						}
					})
				} else {
					uni.request({
						url: buffer.serverIp + '/questionnaires/unpublish',
						method: 'POST',
						data: {
							questionnaire_id: id
						},
						success: (res) => {
							if (res.data.status === 200) {
								uni.showToast({
									title: "停止发布",
									icon: 'none'
								})
								setTimeout(() => {
								  // 在这里编写需要延迟执行的代码
								  uni.redirectTo({
								  	url: '/pages/index/index'
								  })
								}, 1000);
							}
						}
					})
				}

			},
			actionsClick2(id) {
				uni.request({
					url: buffer.serverIp + '/questionnaires/delete',
					method: 'POST',
					data: {
						questionnaire_id: id
					},
					success: (res) => {
						if (res.data.status === 200) {
							uni.reLaunch({
								url: '/pages/index/index'
							})
						}
					}
				})
			},
			actionsClick3(id) {
				uni.navigateTo({
					url: '/pages/analysis/analysis?id=' + id
				})
			}
		}
	}
</script>

<style scoped>
	.index {
		padding-top: 10rpx;
		background-color: #f0f0f0;
		min-height: 100vh;
	}
	
	.content{
		/* padding-bottom: 10px; */
	}
	.popup-content {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		/* padding: 15px; */
		height: 60px;
		background-color: #fff;
	}

	.popup-height {
		/* @include height; */
		width: 200px;
	}

	.text {
		font-size: 12px;
		color: #333;
	}

	.card-parent {
		width: 750rpx;
		height: 75rpx;
	}

	.card-actions {
		display: flex;
		flex-direction: row;
	}

	.card-actions-item {
		text-align: center;
		display: flex;
		flex-direction: column;
		width: 33.3%;
	}

	.card-actions-item-text {
		font-size: 15px;
		color: #a1a1a1;
	}
</style>