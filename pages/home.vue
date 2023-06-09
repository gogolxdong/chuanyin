<template>
	<view>
		<uni-popup ref="popup" :is-mask-click="false">
			<view class="popup-content">
				<image src="@/static/img/walkman.png"></image>
				<view class="h1">
					马上到来，敬请期待
				</view>
				<view class="text">
					尊敬的用户您好，该功能正在研发测试中
					敬请期待！
				</view>
				<view class="close" @click="pophide">
					关闭
				</view>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showPop: 1,
			}
		},
		onLoad() {},
		onShow(){
			uni.showTabBar();
			console.log('保障底部菜单展示')
		},
		mounted() {

			if (this.showPop == 1) {
				this.popshow()
			}
		},
		methods: {
			popshow() {
				this.$refs.popup.open()
			},
			pophide() {
				uni.switchTab({
					url: '/pages/member'
				});
			},
			goTo(e) {
				uni.navigateTo({
					url: e
				})
			}
		}
	}
</script>

<style lang="less">
		.popup-content {
			position: relative;
			width: 560rpx;
			height: 540rpx;
			padding: 0 30rpx;
			box-sizing: border-box;
			border-radius: 20rpx;
			background: url(@/static/img/popbg.png);
			background-size: cover;
			text-align: center;

			image {
				position: absolute;
				top: -100rpx;
				left: 0;
				right: 0;
				margin: 0 auto;
				width: 252rpx;
				height: 270rpx;
			}

			.h1 {
				padding-top: 185rpx;
				color: #ffffff;
				font-size: 40rpx;
				font-weight: 600;
			}

			.text {
				margin-top: 35rpx;
				font-size: 28rpx;
				color: #ffffff;
			}

			.close {
				width: 100%;
				height: 80rpx;
				line-height: 80rpx;
				margin: 60rpx auto 0 auto;
				border-radius: 45rpx;
				background-color: #1e7ef1;
				color: #ffffff;
				font-size: 32rpx;
				text-align: center;
			}
		}
</style>