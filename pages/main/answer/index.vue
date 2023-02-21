<template>
	<view class="container">
		<view class="action">
			<view class="tips">
				官方接口通道
			</view>
			<view class="switch">
				<u-switch activeColor="#26B3A0" :value="true"></u-switch>
			</view>
		</view>
		<view class="answer">
			<view class="ask">{{text}}</view>
			<view class="content">
				{{message}}
			</view>
		</view>
		
		<view class="sheet">
			<view class="btn">
				<u-button open-type='contact' color="#26B3A0" :plain="true" text="开通会员"></u-button>
			</view>
			<view class="btn">
				<u-button @click="handleCopy" iconColor="#ffffff" color="#26B3A0" icon="file-text" text="复制问题答案">
				</u-button>
			</view>
		</view>
			<u-toast ref="uToast"></u-toast>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				message: '',
				text: '',
				openid:'',
			};
		},
		async onLoad({
			text,openid
		}) {
			this.text = text
			this.openid = openid
			const that = this
			that.message = 'API返回内容'
		},
		methods: {
			handleCopy() {
				const that = this
				uni.setClipboardData({
					data: that.message,
					success: function() {
					}
				})
			}
		},
	}
</script>

<style lang="scss">
	.answer {
		width: 90%;
		margin: 0 auto;
		margin-top: 200rpx;
		z-index: -1;

		.ask {
			font-size: 36rpx;
			font-weight: bolder;
		}

		.content {
			margin-top: 30rpx;
			color: #606266;
		}
	}

	.action {
		width: 92%;
		position: fixed;
		bottom: 0rpx;
		top: 0rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		height: 80rpx;
		align-items: center;
		box-shadow: 10rpx 10rpx 10rpx #eee;
		padding: 30rpx;
		z-index: 999;
		background-color: #fff;
	}

	.sheet {
		width: 100%;
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		bottom: env(safe-area-inset-bottom);
		padding: 30rpx 0rpx;
		background-color: #fff;
		box-shadow: -10rpx -10rpx 10rpx #eee;

		.btn {
			width: 42%;

			.u-button {
				height: 90rpx;
			}
		}
	}
</style>
