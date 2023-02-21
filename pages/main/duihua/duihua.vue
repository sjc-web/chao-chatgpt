<template>
	<view>
		<view class="tips color_fff size_12 align_c" :class="{ 'show':ajax.loading }" @tap="getHistoryMsg">
			{{ajax.loadText}}
		</view>
		<u-notice-bar bgColor='#26B3A0' color="#fff" text="由于服务人数过多,如果出现异常建议重新尝试,AI对话已支持上下文连续对话欢迎体验" mode="closable">
		</u-notice-bar>
		<view class="box-1" id="list-box">
			<view class="talk-list">
				<view v-for="(item,index) in talkList" @click="handleCopy(item.content)" :key="index"
					:id="`msg-${item.id}`">
					<view class="item flex_col" :class=" item.type == 1 ? 'push':'pull' ">
						<image :src="item.pic" mode="aspectFill" class="pic"></image>
						<view class="content">{{item.content}}</view>
					</view>
				</view>
			</view>
		</view>
		<view class="box-2">
			<view class="flex_col">
				<view @click="handleopen">
					<u-icon name="grid" color="#000" size="28"></u-icon>
				</view>
				<view class="flex_grow">
					<input type="text" class="content" v-model="content" placeholder="请输入聊天内容"
						placeholder-style="color:#DDD;" :cursor-spacing="6">
				</view>
				<button class="send" @tap="send">发送</button>
			</view>
		</view>
		<u-popup :show="show" :round="10" mode="bottom" @close="close" closeable>
			<view class="UPOP">
				<view class="title ">我的菜单栏</view>
				<view class="margin">
					<view class="userInfo">
						<view class="info">
							<view class="vip" v-if="isday">会员</view>
							<view class="name " v-else>普通用户</view>
							<view class="id ">{{'ID: '+ 1}}</view>
						</view>
					</view>
					<view class="stat ">
						<view class="have ">{{'拥有额度：'+isnum+'次'}}</view>
						<view class="free ">{{'每日免费额度：'+5+'次'}}</view>
					</view>
					<view class="invite ">
						<view class="info ">
							<view class="inviteTitle ">邀请新的小伙伴来体验</view>
							<view class="award ">奖励<text class="span ">{{3}}</text>次/人，每天最多<text
									class="span ">{{10}}</text>人</view>
							<view class="hint ">提示：点击右上角<text class="more ">···</text>可分享朋友圈邀请</view>
						</view>
						<view class="btn ">邀请朋友<button openType="share">邀请</button>
						</view>
					</view>
					<u-line></u-line>
					<view class="videoAd">
						<view class="info">
							<view class="videoAdTitle ">看视频广告攒次数</view>
							<view class="award ">奖励<text class="span ">{{3}}</text>次/个</view>
						</view>
						<view class="btn" @click="handleopdate">观看视频</view>
					</view>
				</view>
				<u-button open-type='contact' color="#26B3A0" :plain="true" text="开通会员"></u-button>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				talkList: [],
				ajax: {
					flag: true, // 请求开关
					loading: true, // 加载中
					loadText: '正在获取消息'
				},
				content: '',
				show: false,
				isday: 0,
				isnum: 5
			}
		},
		async onLoad() {
			const that = this
		},
		onShareAppMessage: function(options) {
			const that = this;
			// 设置菜单中的转发按钮触发转发事件时的转发内容
			const shareObj = {
				title: '一键AI问答',
				imageUrl: '/static/wenda.png', //自定义图片路径，可以是本地文件路径、代码包文件路径或者网络图片路径，支持PNG及JPG，不传入 imageUrl 则使用默认截图。显示图片长宽比是 5:4
				path: '/pages/main/duihua/duihua',
			};
			// 返回shareObj
			return shareObj;
		},
		onShareTimeline: function() {
			const that = this;
			// 设置菜单中的转发按钮触发转发事件时的转发内容
			const shareObj = {
				title: '一键AI问答',
				imageUrl: '/static/wenda.png', //自定义图片路径，可以是本地文件路径、代码包文件路径或者网络图片路径，支持PNG及JPG，不传入 imageUrl 则使用默认截图。显示图片长宽比是 5:4
				path: '/pages/main/duihua/duihua',
			};
			// 返回shareObj
			return shareObj;
		},
		mounted() {
			this.$nextTick(() => {
				this.getHistoryMsg();
			});
		},
		methods: {
			close() {
				this.show = false
			},
			handleopen() {
				this.show = true
			},
			handleCopy(message) {
				const that = this
				uni.setClipboardData({
					data: message,
					success: function() {}
				})
			},
			// 获取历史消息
			getHistoryMsg() {
				if (!this.ajax.flag) {
					return; //
				}
				// 此处用到 ES7 的 async/await 知识，为使代码更加优美。不懂的请自行学习。
				let get = async () => {
					this.hideLoadTips();
					this.ajax.flag = false;
					let data = [{
						"id": '1', // 消息的ID
						"content": `GPT智能AI为您服务：\n1. 知乎百度答题、做作业题目\n2. 写代码、写文案、写论文，写小说\n3. 文案润色、翻译、写诗作词\n4. 扮演面试官、扮演书籍电影角色\n例1：写一篇工作日报我是行政\n例2：帮我写作业，题目是xxx\n例3：把下文翻译成英文：xxx\n例4：写一出能活的短视频剧本\n例5：英文写物理相关的论文\n例6：用王小波的风格写篇情书\n
或者可以问我其他问题\n越完整的描述，我越精确`, // 消息内容
						"type": 0, // 此为消息类别，设 1 为发出去的消息，0 为收到对方的消息,
						"pic": "https://wx.qlogo.cn/mmhead/Q3auHgzwzM55kOdw21icYsGmEojHmk3XMqRLWIZPkZJ6uBLvqdf6SxA/0"
					}];
					const selector = `#msg-${data[data.length-1].id}`;
					// 将获取到的消息数据合并到消息数组中
					this.talkList = [...data, ...this.talkList];
					// 数据挂载后执行，不懂的请自行阅读 Vue.js 文档对 Vue.nextTick 函数说明。
					this.$nextTick(() => {
						// 设置当前滚动的位置
						this.setPageScrollTo(selector);
						this.hideLoadTips(true);
					})
				}
				get();
			},
			// 设置页面滚动位置
			setPageScrollTo(selector) {
				let view = uni.createSelectorQuery().in(this).select(selector);
				view.boundingClientRect((res) => {
					uni.pageScrollTo({
						scrollTop: res.top - 30, // -30 为多显示出大半个消息的高度，示意上面还有信息。
						duration: 0
					});
				}).exec();
			},
			// 隐藏加载提示
			hideLoadTips(flag) {
				if (flag) {
					this.ajax.loadText = '消息获取成功';
					setTimeout(() => {
						this.ajax.loading = false;
					}, 100);
				} else {
					this.ajax.loading = true;
					this.ajax.loadText = '正在获取消息';
				}
			},
			// 发送信息
			async send() {
				const taht = this
				if (taht.isnum < 1) {
					uni.showToast({
						title: '免费额度已用完,请点击右下角获取次数',
						duration: 3000,
						mask: true,
						icon: 'none'
					});
					return
				}
				if (taht.isday === 0) {
					taht.isnum = taht.isnum - 1
				}
				if (!taht.content) {
					uni.showToast({
						title: '请输入有效的内容',
						icon: 'none'
					})
					return;
				}
				let query = ''
				taht.talkList.forEach(item => {
					if (item.type === 1) {
						query += `\nQ: ${item.content}\n`
					}else{
						query += `A: ${item.content}。 <|endoftext|>\n`
					}
				})
				query += `Q: ${taht.content}  + "\nA: `
				// 将当前发送信息 添加到消息列表。
				taht.talkList.push({
					"id": new Date().getTime(),
					"content": taht.content,
					"type": 1,
					"pic": "https://wx.qlogo.cn/mmhead/Q3auHgzwzM55kOdw21icYsGmEojHmk3XMqRLWIZPkZJ6uBLvqdf6SxA/0"
				},{
					"id": new Date().getTime(),
					"content": 'API返回的内容',
					"type": 0,
					"pic": "https://wx.qlogo.cn/mmhead/Q3auHgzwzM55kOdw21icYsGmEojHmk3XMqRLWIZPkZJ6uBLvqdf6SxA/0"
				});
				taht.content = ''
				taht.$nextTick(() => {
					// 清空内容框中的内容
					taht.content = '';
					uni.pageScrollTo({
						scrollTop: 999999, // 设置一个超大值，以保证滚动条滚动到底部
						duration: 0
					});
				})
			}
		}
	}
</script>

<style lang="scss">
	/* 根元素样式 设置页面背景、字体大小、字体颜色，字符间距、长单词换行 */
	page {
		background-color: #f3f3f3;
		font-size: 28rpx;
		box-sizing: border-box;
		color: #333;
		letter-spacing: 0;
		word-wrap: break-word;
	}

	.UPOP {
		padding: 30rpx;

		.title {
			align-items: center;
			display: flex;
			font-size: 30rpx;
			font-weight: 450;
			height: 90rpx;
			justify-content: center;
		}

		.margin {
			margin: auto;

			.userInfo {
				align-items: center;
				display: flex;
				margin-bottom: 30rpx;
			}

			.vip {
				color: #26B3A0;
			}

			.userInfo .info {
				color: #3a3a3a;
				display: flex;
				flex-direction: column;
				font-size: 29rpx;
				justify-content: center;
			}

			.userInfo .info .name {
				color: #3a3a3a;
				font-size: 30rpx;
			}

			.userInfo .info .id {
				color: #3a3a3a;
				font-weight: 450;
			}

			.stat {
				align-items: center;
				color: #26B3A0;
				display: flex;
				font-size: 27rpx;
				margin-bottom: 30rpx;

				.free,
				.have {
					background-color: rgba(81, 181, 237, .1);
					border-radius: 7rpx;
					padding: 13rpx;
				}

				.free {
					margin-left: 30rpx;
				}
			}

			.invite {
				align-items: center;
				display: flex;
				justify-content: space-between;
				margin-bottom: 10rpx;

				.info {
					line-height: 50rpx;

					.inviteTitle {
						color: #3a3a3a;
						font-size: 30rpx;
						font-weight: 450;
					}

					.award {
						color: #3a3a3a;
						font-size: 28rpx;
					}

					.award .span {
						color: #26B3A0;
						margin-left: 10rpx;
						margin-right: 10rpx;
					}

					.hint {
						align-items: center;
						color: #ababab;
						display: flex;
						font-size: 25rpx;
					}

					.hint .more {
						font-size: 40rpx;
						margin-left: 5rpx;
						margin-right: 5rpx;
					}

				}

				.btn {
					align-items: center;
					background-color: rgba(81, 181, 237, .1);
					border-radius: 50rpx;
					color: #26B3A0;
					display: flex;
					font-size: 28rpx;
					height: 60rpx;
					justify-content: center;
					padding-left: 25rpx;
					padding-right: 25rpx;
					position: relative;

					button {
						button: 0;
						left: 0;
						opacity: 0;
						position: absolute;
						right: 0;
						top: 0;
					}
				}
			}

			.videoAd {
				align-items: center;
				display: flex;
				justify-content: space-between;
				margin-bottom: 10rpx;
				margin-top: 15rpx;

				.info {
					line-height: 50rpx;

					.videoAdTitle {
						color: #3a3a3a;
						font-size: 30rpx;
						font-weight: 450;
					}

					.award {
						color: #3a3a3a;
						font-size: 28rpx;
					}

					.award .span {
						color: #26B3A0;
						margin-left: 10rpx;
						margin-right: 10rpx;
					}
				}

				.btn {
					align-items: center;
					background-color: rgba(81, 181, 237, .1);
					border-radius: 50rpx;
					color: #26B3A0;
					display: flex;
					font-size: 28rpx;
					height: 60rpx;
					justify-content: center;
					padding-left: 25rpx;
					padding-right: 25rpx;
					position: relative;
				}
			}
		}
	}

	/* 按钮样式处理 */
	button {
		font-size: 28rpx;
	}

	/* 取消按钮默认的边框线效果 */
	button:after {
		border: none;
	}

	/* 设置图片默认样式，取消默认尺寸 */
	image {
		display: block;
		height: auto;
		width: auto;
	}

	/* 输入框默认字体大小 */
	input {
		font-size: 28rpx;
	}

	/* 列式弹性盒子 */
	.flex_col {
		display: flex;
		flex-direction: row;
		flex-wrap: nowrap;
		justify-content: flex-start;
		align-items: center;
		align-content: center;
	}

	/* 行式弹性盒子 */
	.flex_row {
		display: flex;
		flex-direction: column;
		flex-wrap: nowrap;
		justify-content: flex-start;
		align-items: flex-start;
		align-content: flex-start;
	}

	/* 弹性盒子弹性容器 */
	.flex_col .flex_grow {
		width: 0;
		flex-grow: 1;
	}

	.flex_row .flex_grow {
		flex-grow: 1;
	}

	/* 字体颜色 */
	.color_fff {
		color: #fff;
	}

	/* 字体大小 */

	.size_12 {
		font-size: 24rpx;
	}

	/* 对齐方式 */
	.align_c {
		text-align: center;
	}

	/* 加载数据提示 */
	.tips {
		position: fixed;
		left: 0;
		top: var(--window-top);
		width: 100%;
		z-index: 9;
		background-color: rgba(0, 0, 0, 0.15);
		height: 72rpx;
		line-height: 72rpx;
		transform: translateY(-80rpx);
		transition: transform 0.3s ease-in-out 0s;

		&.show {
			transform: translateY(0);
		}
	}

	.box-1 {
		width: 100%;
		height: auto;
		padding-bottom: 100rpx;
		box-sizing: content-box;

		/* 兼容iPhoneX */
		margin-bottom: 0;
		margin-bottom: constant(safe-area-inset-bottom);
		margin-bottom: env(safe-area-inset-bottom);
	}

	.box-2 {
		position: fixed;
		left: 0;
		width: 100%;
		bottom: 0;
		height: auto;
		z-index: 2;
		border-top: #e5e5e5 solid 1px;
		box-sizing: content-box;
		background-color: #F3F3F3;

		/* 兼容iPhoneX */
		padding-bottom: 0;
		padding-bottom: constant(safe-area-inset-bottom);
		padding-bottom: env(safe-area-inset-bottom);

		>view {
			padding: 0 20rpx;
			height: 100rpx;
		}

		.content {
			background-color: #fff;
			height: 64rpx;
			padding: 0 20rpx;
			border-radius: 32rpx;
			font-size: 28rpx;
		}

		.send {
			background-color: #42b983;
			color: #fff;
			height: 64rpx;
			margin-left: 20rpx;
			border-radius: 32rpx;
			padding: 0;
			width: 120rpx;
			line-height: 62rpx;

			&:active {
				background-color: #5fc496;
			}
		}
	}

	.talk-list {
		padding-bottom: 20rpx;

		/* 消息项，基础类 */
		.item {
			padding: 20rpx 20rpx 0 20rpx;
			align-items: flex-start;
			align-content: flex-start;
			color: #333;

			.pic {
				width: 92rpx;
				height: 92rpx;
				border-radius: 50%;
				border: #fff solid 1px;
			}

			.content {
				padding: 20rpx;
				border-radius: 4px;
				max-width: 500rpx;
				word-break: break-all;
				line-height: 52rpx;
				position: relative;
			}

			/* 收到的消息 */
			&.pull {
				.content {
					margin-left: 32rpx;
					background-color: #fff;
					white-space: pre-wrap;

					&::after {
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-right: 20rpx solid #fff;
						position: absolute;
						top: 30rpx;
						left: -18rpx;
					}
				}
			}

			/* 发出的消息 */
			&.push {
				/* 主轴为水平方向，起点在右端。使不修改DOM结构，也能改变元素排列顺序 */
				flex-direction: row-reverse;

				.content {
					margin-right: 32rpx;
					background-color: #a0e959;

					&::after {
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-left: 20rpx solid #a0e959;
						position: absolute;
						top: 30rpx;
						right: -18rpx;
					}
				}
			}
		}
	}
</style>
