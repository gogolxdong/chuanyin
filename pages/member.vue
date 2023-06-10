<template>
	<view class="member">
		<image src="@/static/img/stars.png" class="stars"></image>
		<image src="@/static/img/moon.png" class="moon"></image>
		<view class="content">
			<view class="head">
				<view class="left">
					<view class="nickname" @click="goTo('/pages/info')">
						游客018238dfads
					</view>
					<view class="other">
						<view class="level">
							LEVEL 4
						</view>
						<view class="score">
							<image src="@/static/img/score.png"></image>
							积分：4000
						</view>
					</view>
				</view>
				<image src="@/static/img/man.png" @click="goTo('/pages/info')"></image>
			</view>
			<view class="pannel">
				<view class="item" @click="goTo('/pages/info')">
					<image src="@/static/img/i1.png"></image>
					<view>
						基本信息
						<text>></text>
					</view>
				</view>
				<view class="item" @click="popshow">
					<image src="@/static/img/i2.png"></image>
					<view>
						账户充值
						<text>></text>
					</view>
				</view>
				<view class="item" @click="goTo('/pages/changepwd')">
					<image src="@/static/img/i3.png"></image>
					<view>
						修改密码
						<text>></text>
					</view>
				</view>
				<view class="item" @click="popshow">
					<image src="@/static/img/i4.png"></image>
					<view>
						积分管理
						<text>></text>
					</view>
				</view>
				<view class="item" @click="inviteshow">
					<image src="@/static/img/i5.png"></image>
					<view>
						邀请码
						<text>></text>
					</view>
				</view>
				<view class="item" @click="goTo('/')">
					<image src="@/static/img/i6.png"></image>
					<view>
						退出登录
						<text>></text>
					</view>
				</view>
			</view>
		</view>
		<uni-popup ref="popup">
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
		<uni-popup ref="invite">
			<view class="invite-content">
				<view class="h1">
					邀请链接分享
				</view>
				<view class="text">
					复制邀请链接发给好友一起加入我们吧!
				</view>
				<view class="inviteurl">
					{{inviteurl}}
				</view>
				<view class="button">
					<button @click="invitehide" class="close">关闭</button>
					<button @click="copy" class="copy">复制链接</button>
				</view>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showPop: 0,
				inviteurl: 'www.datacats.cn',
			}
		},
		onLoad() {},
		onShow(){
			uni.showTabBar();
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
				this.$refs.popup.close()
			},
			inviteshow() {
				this.$refs.invite.open()
			},
			invitehide() {
				this.$refs.invite.close()
			},
			goTo(e) {
				uni.navigateTo({
					url: e
				})
			},
			copy() {
				let result
				// #ifndef H5
				//uni.setClipboardData方法就是讲内容复制到粘贴板
				uni.setClipboardData({
					data: this.inviteurl, //要被复制的内容
					success: () => { //复制成功的回调函数
						uni.showToast({ //提示
							title: '复制成功'
						})
					}
				});
				// #endif

				// #ifdef H5 
				let textarea = document.createElement("textarea")
				textarea.value = this.inviteurl
				textarea.readOnly = "readOnly"
				document.body.appendChild(textarea)
				textarea.select() // 选中文本内容
				textarea.setSelectionRange(0, textarea.value.length)
				uni.showToast({ //提示
					title: '复制成功'
				})
				result = document.execCommand("copy")
				textarea.remove()
				// #endif
			}
		}
	}
</script>

<style lang="less">
	.member {
		position: relative;
		width: 100%;
		height: 100vh;
		background-color: #17171f;
		overflow: hidden;

		.stars {
			position: absolute;
			top: 90rpx;
			left: 50rpx;
			width: 146rpx;
			height: 68rpx;
		}

		.moon {
			position: absolute;
			top: 0;
			right: 0;
			width: 300rpx;
			height: 251rpx;
		}

		.content {
			padding-top: 130rpx;

			.head {
				display: flex;
				justify-content: space-between;

				.left {
					width: calc(100% - 180rpx);
					margin-top: 20rpx;

					.nickname {
						color: #ffffff;
						font-size: 44rpx;
						font-weight: 600;
					}

					.other {
						display: flex;
						justify-content: start;
						margin-top: 24rpx;
						font-size: 22rpx;

						.level {
							padding: 0 20rpx;
							border-radius: 18rpx;
							height: 36rpx;
							line-height: 36rpx;
							color: #ffffff;
							background-color: #1e7cf2;
							border: 2rpx solid #1e7cf2;
							margin-right: 20rpx;
						}

						.score {
							color: #4c9bff;
							height: 36rpx;
							line-height: 36rpx;
							border: 2rpx solid #1e7cf2;
							padding: 0 20rpx;
							border-radius: 18rpx;

							image {
								position: relative;
								top: 2rpx;
								width: 20rpx;
								height: 22rpx;
								margin-right: 6rpx;
							}
						}
					}
				}

				>image {
					width: 163rpx;
					height: 163rpx;
					border-radius: 50%;
					border: 4rpx solid #eeeeee;
				}
			}

			.pannel {
				background-color: #24253e;
				padding: 0 24rpx;
				margin-top: 80rpx;
				border-radius: 10rpx;

				.item {
					height: 108rpx;
					line-height: 108rpx;
					font-size: 30rpx;
					color: #ffffff;
					display: flex;
					align-items: center;

					image {
						width: 40rpx;
						height: 40rpx;
						margin-right: 20rpx;
					}

					view {
						width: calc(100% - 60rpx);
						border-bottom: 1rpx solid #38384e;

						text {
							float: right;
						}
					}

					&:last-child view {
						border-bottom: none;
					}
				}
			}
		}

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

		.invite-content {
			position: relative;
			width: 560rpx;
			min-height: 525rpx;
			padding: 60rpx 45rpx;
			box-sizing: border-box;
			border-radius: 20rpx;
			background: url(@/static/img/invite.png);
			background-size: cover;
			text-align: center;

			.h1 {
				color: #ffffff;
				font-size: 40rpx;
				font-weight: 600;
				margin-top: 5rpx;
			}

			.text {
				margin-top: 35rpx;
				font-size: 28rpx;
				color: #ffffff;
			}
			.inviteurl{
				box-sizing: border-box;
				padding: 30rpx;
				width: 100%;
				height: 120rpx;
				border-radius: 5rpx;
				line-height: 40rpx;
				background-color: #2f3f5f;
				font-size: 24rpx;
				color: #b6b7cc;
				margin-top: 50rpx;
				margin-bottom: 60rpx;
			}
			.button{
				display: flex;
				justify-content: space-between;
				button{
					box-sizing: border-box;
					border-radius: 40rpx;
					width: 220rpx;
					height: 80rpx;
					line-height: 80rpx;
					font-size: 32rpx;
					text-align: center;
				}
				.close{
					background-color: transparent;
					border: 2rpx solid #1e7cf2;
					color: #1e7cf2;
				}
				.copy{
					background-color: #1e7cf2;
					color: #ffffff;
				}
			}
		}
	}
</style>