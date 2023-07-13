<template>
	<view class="Questionnaire">
		<view class="title">
			<uni-title type="h1" :title="title"></uni-title>
			<view class="introduce"> {{introduce}} </view>
		</view>

		<view class="content" v-for="(item,index) in this.dataArr" :index="index" :key="index">

			<uni-card v-if="item.type==='gap'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<uni-easyinput></uni-easyinput>
				</uni-section>
				<view class="horizontal-line"></view>
				<view slot="actions" class="card-parent card-actions">
					<view class="card-actions-item" @click="actionsClick1(index)">
						<uni-icons custom-prefix="iconfont" type="icon-bianji" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">编辑</text>
					</view>
					<view class="card-actions-item" @click="actionsClick2(index)">
						<uni-icons custom-prefix="iconfont" type="icon-shanchu" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">删除</text>
					</view>
				</view>
			</uni-card>
			<uni-card v-else-if="item.type==='single'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<view class="uni-px-5 uni-pb-5">
						<uni-data-checkbox :localdata="item.choices"></uni-data-checkbox>
					</view>
				</uni-section>
				<view class="horizontal-line"></view>
				<view slot="actions" class="card-parent card-actions">
					<view class="card-actions-item" @click="actionsClick1(index)">
						<uni-icons custom-prefix="iconfont" type="icon-bianji" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">编辑</text>
					</view>
					<view class="card-actions-item" @click="actionsClick2(index)">
						<uni-icons custom-prefix="iconfont" type="icon-shanchu" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">删除</text>
					</view>
				</view>
			</uni-card>
			<uni-card v-else-if="item.type==='multi'">
				<uni-section :title="index+1 + '.' + item.title" type="circle">
					<view class="uni-px-5 uni-pb-5">
						<uni-data-checkbox multiple :localdata="item.choices"></uni-data-checkbox>
					</view>
				</uni-section>
				<view class="horizontal-line"></view>
				<view slot="actions" class="card-parent card-actions">
					<view class="card-actions-item" @click="actionsClick1(index)">
						<uni-icons custom-prefix="iconfont" type="icon-bianji" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">编辑</text>
					</view>
					<view class="card-actions-item" @click="actionsClick2(index)">
						<uni-icons custom-prefix="iconfont" type="icon-shanchu" size="20" color="#999"></uni-icons>
						<text class="card-actions-item-text">删除</text>
					</view>
				</view>
			</uni-card>
		</view>
		<button @tap="preLook">预览</button>
		<button @tap="submit">保存</button>
		<uni-fab ref="fab" :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical"
			:direction="direction" @trigger="trigger" @fabClick="fabClick" />
	</view>
</template>

<script>
	import buffer from '../../common/buffer.js'
	export default {
		data() {
			return {
				title: '',
				introduce: '',
				directionStr: '垂直',
				horizontal: 'left',
				vertical: 'bottom',
				direction: 'horizontal',
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
					buttonColor: '#007AFF',
					iconColor: '#fff'
				},
				is_color_type: false,
				content: [{
						iconPath: '/static/imgs/danxuanti.png',

						text: '单选',
						active: false
					},
					{
						iconPath: '/static/imgs/duoxuanti.png',
						text: '多选',
						active: false
					},
					{
						iconPath: '/static/imgs/tiankongti.png',
						text: '填空',
						active: false
					}
				],
				dataArr: [],
				questionnaireId: -1
			}
		},
		onLoad: function(e) {
			//将问卷名和问卷简介放入全局变量中，以便重新加载时能获得正确的头部名称
			// console.log(this.title);

			buffer.questionnaireTitle.title = e.name;
			buffer.questionnaireTitle.introduce = e.introduce;

			//将标题和简介放入本页的数据中
			this.title = buffer.questionnaireTitle.title;
			this.introduce = buffer.questionnaireTitle.introduce;

			//将新加的选项放入本页的数据中
			this.dataArr = buffer.questionnaire;
			console.log("加载界面的dataArr：", this.dataArr);
		},
		onBackPress() {
			if (this.$refs.fab.isShow) {
				this.$refs.fab.close()
				return true
			}
			return false
		},
		methods: {
			trigger(e) {
				if (e.index === 0) {
					uni.navigateTo({
						url: '../singleChoice/singleChoice'
					})
				} else if (e.index === 1) {
					uni.navigateTo({
						url: '../multiChoice/multiChoice'
					})
				} else {
					uni.navigateTo({
						url: '../gapFilling/gapFilling'
					})
				}
			},
			preLook() {
				console.log(buffer.questionnaire)
				uni.navigateTo({
					url: '../display/display'
				})
			},
			submit() {
				uni.request({
					url: buffer.serverIp+'/questionnaires/create',
					method: 'POST',
					data: {
						title: this.title,
						description: this.introduce,
						creator_id:buffer.userId
						// 将要传递给后端的其他数据
					},
					success:(res)=> {
						// 请求成功的处理逻辑
						console.log('请求成功', res.data);
						this.questionnaireId = res.data.data.questionnaire_id;

						// buffer.questionnaireList.push({
						// 	title:this.title,
						// 	introduce:this.introduce,
						// 	questionnnaireId:this.questionnaireId,
						// 	released:false
						// })
						console.log(this.questionnaireId);
						console.log(this.dataArr);
						uni.request({
							url: buffer.serverIp+'/questions/create',
							method: 'POST',
							data: {
								questionnaire_id: this.questionnaireId,
								quesoptns: this.dataArr
								// 将要传递给后端的其他数据
							},
							success:(res)=>  {
								// 请求成功的处理逻辑
								console.log('请求成功', res.data);
								if(res.data.status===200){
									// buffer.questionnaireList.push({
									// 	title:this.title,
									// 	introduce:this.introduce,
									// 	questionnaireId:this.questionnaireId,
									// 	released:false
									// });
									buffer.questionnaire=[];
									buffer.questionnaireTitle={
										title:'',
										introduce:''
									};
									uni.reLaunch({
										url:'/pages/index/index'
									})
								}
							},
							fail: (err)=> {
								// 请求失败的处理逻辑
								console.error('请求失败', err);
							}
						});
					},
					fail: (err)=> {
						// 请求失败的处理逻辑
						console.error('请求失败', err);
					}
				});
				// buffer.questionnaireList.push({
				// 	title: this.title,
				// 	introduce: this.introduce,
				// 	questionnaireId: this.questionnaireId,
				// 	released: false
				// });
				// uni.navigateBack({
				// 	delta: 2
				// })
			},
			actionsClick1(e) {
				// console.log(this.dataArr[e].type)
				let type = this.dataArr[e].type;
				switch (type) {
					case 'single':
						uni.navigateTo({
							url: '/pages/singleChoice/singleChoice?index=' + e
						});
					case 'multi':
						uni.navigateTo({
							url: '/pages/multiChoice/multiChoice?index=' + e
						});
					case 'gap':
						uni.navigateTo({
							url: '/pages/gapFilling/gapFilling?index=' + e
						});
				}
			},
			actionsClick2(e) {
				this.dataArr.splice(e, 1);
				buffer.questionnaire.splice(e, 1);
			}
		}
	}
</script>


<style lang="scss">
	.title {
		padding: 20px 20px 0 20px;

		.introduce {
			font-size: 16px;
			color: #a1a1a1;
		}
	}

	button {
		margin: 15px;
		background-color: #00aaff;
		color: #fff;
	}

	.horizontal-line {
		border-top: 1px solid #dcdcdc;
		/* 设置上边框为实线，颜色为黑色 */
		// padding-top: 10px 0;
		margin-top: 10px;
		/* 设置上下间距，根据需要调整 */
	}

	.card-parent {
		width: 650upx;
		height: 75upx;
	}

	.card-actions {
		display: flex;
		justify-content: space-between;
		// align-items: center;
		padding-top: 15rpx;
		// display: flex;
		// flex-direction: row; //设置布局方向为水平
	}

	.card-actions-item {
		margin-top: 10rpx;
		text-align: center;
		width: 50%; //每个元素宽度占比父类的三分之一
		// height: 75upx;
		// line-height: 150upx;
	}

	.card-actions-item-text {
		font-size: 15px;
		color: #a1a1a1;
	}
</style>