<template>
	<view class="container">
		<view class="bg"></view>
		<view class="form">
			</u-notice-bar>
			<view class="header">
				<view class="title">
					<view>人工智能回答你需要的问题</view>
					<view>每天免费额度5次/剩余{{ isnum }}次</view>
				</view>
			</view>
			<u-transition :show="true" mode="fade-left">
				<view class="panel">
					<u-cell isLink :border="false" title="填写您的问题" :value="isvalue" clickable @click="handleopen">
					</u-cell>
					<u-picker :show="picshow" :columns="columns" keyName="label" @cancel="handlecancel"
						@confirm="handleconfirm"></u-picker>
					<view class="textarea">
						<u--textarea v-model="text" height="140" placeholder="请输入您的问题"></u--textarea>
					</view>
					<view class="btn-group">
						<view class="btn paste">
							<u-button icon="file-text" text="粘贴问题" @click="handlezt"></u-button>
						</view>
						<view class="btn get">
							<u-button @click="onSubmitGPT" iconColor="#ffffff" color="#26B3A0" icon="edit-pen"
								text="获取问题答案"></u-button>
						</view>
					</view>
				</view>
			</u-transition>
		</view>
		<view class="alert">
			<u-alert title="当前使用人数过多,获取答案可能超过30秒钟" type="error"></u-alert>
		</view>
		<view class="tool">
			<view class="header">
				<view class="title">功能特性</view>
			</view>
			<u-transition :show="true" mode="slide-up">
				<view class="grid">
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/1.png">
							</u-icon>
						</view>
						<view class="title">AI回答</view>
						<view class="desc">轻松获取</view>
					</view>
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/2.png">
							</u-icon>
						</view>
						<view class="title">智能答案</view>
						<view class="desc">免去千篇一律</view>
					</view>
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/3.png">
							</u-icon>
						</view>
						<view class="title">自动代码</view>
						<view class="desc">免去繁琐搜索</view>
					</view>
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/4.png">
							</u-icon>
						</view>
						<view class="title">引导写作</view>
						<view class="desc">AI发散思维</view>
					</view>
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/5.png">
							</u-icon>
						</view>
						<view class="title">存在限制</view>
						<view class="desc">会有偏见内容</view>
					</view>
					<view class="item">
						<view class="icon">
							<u-icon size="28"
								name="/static/6.png">
							</u-icon>
						</view>
						<view class="title">在线工具</view>
						<view class="desc">无需下载软件</view>
					</view>
				</view>
			</u-transition>
		</view>
		<view class="share">
			<u-button openType='share' shape="circle" color="#26B3A0" :plain="true" icon="share" text="推荐新朋友+3">
			</u-button>
		</view>
		<view class="btnss">
			<u-button shape="circle" :plain="true" color="#26B3A0" text="观看广告+3">
			</u-button>
		</view>
		<view class="btns">
			<u-button @click="handleCopy" shape="circle" :plain="true" color="#26B3A0" icon="file-text" text="复制用户ID">
			</u-button>
		</view>
		<u-modal :show="show" :title="title" :content='content' @confirm="confirm" showCancelButton @cancel="cancel">
		</u-modal>
		<u-toast ref="uToast"></u-toast>
	</view>
</template>

<script>
	// 在页面中定义激励视频广告
	let videoAd = null
	// 在页面中定义插屏广告
	let interstitialAd = null
	import {
		handlelkist,
		handleopdate,
		getid,
		getlist,
		msgSecCheck
	} from "@/servers/index.js"
	export default {
		data() {
			return {
				picshow: false,
				text: '',
				show: false,
				isvalue: '普通线路',
				type: '1',
				title: '提示',
				columns: [],
				content: '由于AI模型计费,需观看广告后免费使用。',
				isday: 0,
				isnum: 5
			};
		},
		async onLoad() {},
		onShow() {
			const that = this
		},
		onShareAppMessage: function(options) {
			const that = this;
			// 设置菜单中的转发按钮触发转发事件时的转发内容
			const shareObj = {
				title: '一键AI问答',
				imageUrl: '/static/wenda.png', //自定义图片路径，可以是本地文件路径、代码包文件路径或者网络图片路径，支持PNG及JPG，不传入 imageUrl 则使用默认截图。显示图片长宽比是 5:4
				path: '/pages/main/form/index',
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
				path: '/pages/main/form/index',
			};
			// 返回shareObj
			return shareObj;
		},
		methods: {
			handleCopy() {
				const that = this
				uni.setClipboardData({
					data: '3',
					success: function() {}
				})
			},
			handleopen() {
				this.picshow = true
			},
			handlecancel() {
				this.picshow = false
			},
			handleconfirm(data) {
				this.picshow = false
				this.isvalue = data.value[0].label
				this.type = data.value[0].id
			},
			async handlelog() {
				const that = this
				if (that.isnum < 1) {
					uni.showToast({
						title: '免费次数已用完,请观看广告或者邀请好友+次数',
						duration: 3000,
						mask: true,
						icon: 'none'
					});
					return
				}
				if (that.isday === 0) {
					that.isnum = that.isnum - 1
				}
				uni.navigateTo({
					url: '/pages/main/answer/index?text=' + that.text + '&openid=' + 1
				})
				return
			},
			cancel() {
				this.show = false;
			},
			confirm() {
				this.show = false;
				if (videoAd) {
					videoAd.show().catch(() => {
						// 失败重试
						videoAd.load()
							.then(() => videoAd.show())
							.catch(err => {
								console.log('激励视频 广告显示失败')
							})
					})
					return
				}
			},
			handlezt() {
				const that = this
				uni.getClipboardData({
					success: function(res) {
						that.text = res.data
					}
				})
			},
			onSubmitGPT() {
				if (this.text.length === 0) return this.$refs.uToast.show({
					type: 'default',
					message: "请输入问题"
				})
				this.handlelog()
			}
		}
	}
</script>

<style lang="scss" scoped>
	/deep/ .u-cell__body {
		padding: 0 !important;
	}

	.share {
		position: fixed;
		right: 40rpx;
		bottom: 100rpx;
		width: 40%;

		.u-button {
			box-shadow: 0rpx 10rpx 10rpx #eee !important;
		}
	}

	.btns {
		position: fixed;
		right: 40rpx;
		bottom: 220rpx;
		width: 40%;

		.u-button {
			box-shadow: 0rpx 10rpx 10rpx #eee !important;
		}
	}

	.btnss {
		position: fixed;
		right: 40rpx;
		bottom: 340rpx;
		width: 40%;

		.u-button {
			box-shadow: 0rpx 10rpx 10rpx #eee !important;
		}
	}

	.isad {

		// position: fixed;
		// right: 40rpx;
		// bottom: 180rpx;
		// width: 40%;
		.u-button {
			box-shadow: 0rpx 10rpx 10rpx #eee !important;
		}
	}

	.tool {
		width: 90%;
		margin: 15rpx auto;

		.header {
			.title {
				margin-bottom: 30rpx;
			}
		}

		.grid {
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			justify-content: space-between;

			.item {
				width: 30%;
				display: flex;
				flex-direction: column;
				align-items: center;
				background-color: #F0FAF8;
				margin: 15rpx 0rpx;
				padding: 30rpx 0rpx;
				border-radius: 10rpx;

				.title {
					color: #1B2B20;
					margin-top: 15rpx;
				}

				.desc {
					color: #67776C;
					font-size: 24rpx;
				}
			}
		}
	}

	.alert {
		width: 90%;
		margin: 30rpx auto;
	}

	.form {
		width: 90%;
		margin: 0 auto;
		z-index: 999;

		.header {
			margin-bottom: 30rpx;

			.title {
				text-align: center;
				color: #fff;
				margin: 30rpx 0rpx;
				display: flex;
				justify-content: space-between;
			}
		}

		.panel {
			padding: 30rpx;
			background-color: #fff;
			border-radius: 15rpx;
			box-shadow: 0rpx 10rpx 10rpx #eee;

			// .head {
			// 	display: flex;
			// 	flex-direction: row;
			// 	justify-content: space-between;

			// 	.tips {
			// 		color: #dd6161;
			// 	}
			// }

			.textarea {
				margin-top: 30rpx;
			}

			.btn-group {
				display: flex;
				flex-direction: row;
				justify-content: space-between;
				margin-top: 30rpx;

				.paste {
					width: 40%;
				}

				.get {
					width: 56%;
				}
			}
		}
	}

	.bg {
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 100%;
		background-color: #26B3A0;
		min-height: 200rpx;
		border-radius: 0rpx 0rpx 140rpx 140rpx;
		z-index: -1;
	}
</style>
