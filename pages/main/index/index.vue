<template>
	<view class="container">
		<view class="form">
			<view class="logo">
				<u--image src="/static/wxlogo.png" shape="circle" width="200rpx" height="200rpx">
				</u--image>
			</view>
			<u-transition :show="true" mode="slide-left">
				<view class="title">一键AI问答</view>
			</u-transition>
			<u-transition :show="true" mode="slide-right">
				<view class="desc">AI界的新神器，AI帮你写答案！</view>
			</u-transition>
			<view class="btn-group">
				<view class="btn" v-on:click="onToForm">
					<u-button shape="circle" iconColor="#ffffff" color="#26B3A0" icon="edit-pen" text="AI问答(免注册体验)">
					</u-button>
				</view>
				<view class="btn" v-on:click="onToDuihua">
					<u-button shape="circle" iconColor="#ffffff" color="#26B3A0" icon="edit-pen" text="AI对话(免注册体验)">
					</u-button>
				</view>
				<view class="btn" v-on:click="onToDuihuas">
					<u-button shape="circle" iconColor="#ffffff" color="#26B3A0" icon="edit-pen" text="AI对话(已注册)">
					</u-button>
				</view>
				<view class="btn" v-on:click="onToWeb">
					<u-button shape="circle" iconColor="#ffffff" color="#26B3A0" text="注册攻略">
					</u-button>
				</view>
				<view class="btn">
					<u-button open-type='contact' color="#26B3A0" :plain="true" text="咨询客服(业务沟通)"></u-button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			};
		},
		onShareAppMessage: function() {
			return {
				title: '一键AI问答',
				imageUrl: '/static/wenda.png',
			};
		},
		onShareTimeline: function() {
			return {
				title: '一键AI问答',
				imageUrl: '/static/wenda.png',
			};
		},
		methods: {
			onToForm() {
				uni.navigateTo({
					url: '/pages/main/form/index'
				})
			},
			onToDuihua() {
				uni.navigateTo({
					url: '/pages/main/duihua/duihua'
				})
			},
			onToDuihuas() {
				uni.navigateTo({
					url: '/pages/main/duihuas/duihuas'
				})
			},
			onToWeb() {
				uni.navigateTo({
					url: '/pages/main/webview/webview?url=' + encodeURIComponent(
						'https://mp.weixin.qq.com/s?__biz=MzUzMDkzMzIxNA==&mid=2247484359&idx=1&sn=0bb59f3ca5e4e8f20a3e604a8ead5160&chksm=fa4b7776cd3cfe60fb292d4b0aa7d747c4072c130636004706ab2b6457eb5834752bf20210fd&token=1419762871&lang=zh_CN#rd'
					)
				})
			},
		}
	}
</script>

<style lang="scss">
	.form {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		margin-top: 30%;

		.title {
			font-size: 38rpx;
			font-weight: bolder;
			margin-top: 15rpx;
		}

		.desc {
			margin-top: 15rpx;
			font-size: 28rpx;
			color: #666;
		}

		.btn-group {
			width: 80%;

			.btn {
				margin: 30rpx 0rpx;

				.u-button {
					height: 100rpx;
				}
			}
		}
	}
</style>
