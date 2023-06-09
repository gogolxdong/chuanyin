<template>
	<view class="signup">
		<view class="wave">
			<view class="wave1"></view>
			<view class="wave2"></view>
			<view class="wave3"></view>
		</view>
		<view class="content">
			<status-bar />
			<image src="@/static/img/close.png" class="close" @click="back"></image>
			<view class="h1">
				修改密码
			</view>
			<uni-forms class="form" v-show="this.way == 1" ref="phoneForm" :modelValue="phoneForm" :rules="phoneRules">
				<view class="form">
					<uni-forms-item name="phone">
						<view class="item">
							<image src="@/static/img/phone.png" class="phone"></image>
							<view class="input">
								<input maxlength="11" v-model="phoneForm.phone" placeholder="请输入手机号"
									placeholder-style="font-size:28rpx;color:#b6b7cc;" />
							</view>
						</view>
					</uni-forms-item>
					<uni-forms-item name="check">
						<view class="item">
							<image src="@/static/img/check.png" class="check"></image>
							<view class="input">
								<input maxlength="6" v-model="phoneForm.check" placeholder="请输入验证码"
									placeholder-style="font-size:28rpx;color:#b6b7cc;" />
								<view class="getcheck" @click="startCountdown" :disabled="countingDown">
									{{ buttonText }}
								</view>
							</view>
						</view>
					</uni-forms-item>
					<uni-forms-item name="password">
						<view class="item">
							<image src="@/static/img/password.png" class="password"></image>
							<view class="input">
								<input placeholder="请输入新密码" v-model="phoneForm.password" :password="!showPassword"
									placeholder-style="font-size:28rpx;color:#b6b7cc;" />
								<view class="getcheck">
									<image src="@/static/img/sleep.png" v-if="this.showPassword == 0" mode="aspectFit"
										class="showpwd" @click="showpwd"></image>
									<image src="@/static/img/awake.png" v-else mode="aspectFit" class="showpwd"
										@click="showpwd"></image>
								</view>
							</view>
						</view>
					</uni-forms-item>
					<uni-forms-item name="checkpwd">
						<view class="item">
							<image src="@/static/img/password.png" class="password"></image>
							<view class="input">
								<input placeholder="请再次确认新密码" v-model="phoneForm.checkpwd" :password="!showCheckpwd"
									placeholder-style="font-size:28rpx;color:#b6b7cc;" />
								<view class="getcheck">
									<image src="@/static/img/sleep.png" v-if="this.showCheckpwd == 0" mode="aspectFit"
										class="showpwd" @click="showcwd"></image>
									<image src="@/static/img/awake.png" v-else mode="aspectFit" class="showpwd"
										@click="showcwd"></image>
								</view>
							</view>
						</view>
					</uni-forms-item>
					<view class="submit" @click="phoneSubmit">
						提 交
					</view>
				</view>
			</uni-forms>
		</view>
	</view>
</template>

<script>
	import statusBar from "@/uni_modules/uni-nav-bar/components/uni-nav-bar/uni-status-bar.vue";
	export default {
		components: {
			statusBar
		},
		data() {
			return {
				way: 1,
				showPassword: 0,
				showCheckpwd: 0,
				countingDown: false, // 是否正在倒计时
				countdownSeconds: 0, // 倒计时秒数
				buttonText: '获取验证码', // 按钮文本
				phoneForm: {
					phone: '',
					check: '',
					password: '',
					checkpwd: '',
				},
				phoneRules: {
					phone: {
						rules: [{
								required: true,
								errorMessage: '请输入手机号',
							},
							{
								validateFunction: function(rule, value, data, callback) {
									let iphoneReg = /^1[0-9]{10}$/
									if (!iphoneReg.test(value)) {
										callback('手机号码格式不正确，请重新填写')
									}
									return true
								}
							},
						]
					},
					check: {
						rules: [{
								required: true,
								errorMessage: '请填写验证码',
							},
							{
								minLength: 6,
								errorMessage: '请填写正确验证码',
							}
						]
					},
					password: {
						rules: [{
								required: true,
								errorMessage: '请输入密码',
							},
							{
								minLength: 6,
								maxLength: 16,
								errorMessage: '长度在 6 到 12 个字符',
							}
						]
					},
					checkpwd: {
						rules: [{
								required: true,
								errorMessage: '请确认密码',
							},
							{
								minLength: 6,
								maxLength: 16,
								errorMessage: '长度在 6 到 12 个字符',
							},
							{
								validateFunction: (rule, value, data, callback) => {
									// 异步需要返回 Promise 对象
									return new Promise((resolve, reject) => {
										if (value === this.phoneForm.password) {
											// 通过返回 resolve
											resolve()
										} else {
											// 不通过返回 reject(new Error('错误信息'))
											reject(new Error('两次输入密码不一致'))
										}
									})
								}
							}
						]
					}
				}
			}
		},
		onLoad() {

		},
		mounted() {
			// 页面加载时检查存储的时间戳和剩余倒计时时间
			const startTimestamp = parseInt(localStorage.getItem('countdownStartTimestamp') || '0');
			const currentTimestamp = Math.floor(Date.now() / 1000);
			const remainingSeconds = 60 - (currentTimestamp - startTimestamp);

			if (remainingSeconds > 0) {
				this.countingDown = true;
				this.countdownSeconds = remainingSeconds;
				this.startCountdown();
			}
			console.log('刷新倒计时' + this.countdownSeconds);
			this.continueCountdown()
		},
		methods: {
			showpwd() {
				if (this.showPassword == 0) {
					console.log('显示密码')
					this.showPassword = 1
				} else {
					console.log('隐藏密码')
					this.showPassword = 0
				}
			},
			showcwd() {
				if (this.showCheckpwd == 0) {
					console.log('显示密码')
					this.showCheckpwd = 1
				} else {
					console.log('隐藏密码')
					this.showCheckpwd = 0
				}
			},
			phoneSubmit() {
				this.$refs.phoneForm.validate().then(res => {
					console.log('表单验证正确：', res);
					// uni.navigateTo({
					// 	url: '/pages/info'
					// });
				}).catch(err => {
					console.log('表单验证错误：', err);
				})
			},
			back() {
				uni.navigateBack({
					delta: 1, //返回层数，2则上上页
				})
			},
			startCountdown() {
				const startTimestamp = Math.floor(Date.now() / 1000); // 倒计时开始的时间戳
				const remainingSeconds = 60; // 倒计时秒数

				if (this.countingDown) {

					return; // 已在倒计时中，不执行操作
				}

				this.countingDown = true;
				this.countdownSeconds = remainingSeconds;
				this.buttonText = `${this.countdownSeconds}秒后重新获取`;

				// 存储倒计时开始的时间戳和剩余倒计时时间
				localStorage.setItem('countdownStartTimestamp', startTimestamp.toString());
				localStorage.setItem('countdownRemainingSeconds', remainingSeconds.toString());

				this.continueCountdown()
			},
			continueCountdown() {
				const timer = setInterval(() => {
					this.countdownSeconds--;
					if (this.countdownSeconds <= 0) {
						clearInterval(timer);
						this.countingDown = false;
						this.countdownSeconds = 0;
						this.buttonText = '获取验证码';

						// 清除存储的时间戳和剩余倒计时时间
						localStorage.removeItem('countdownStartTimestamp');
						localStorage.removeItem('countdownRemainingSeconds');
					} else {
						this.buttonText = `${this.countdownSeconds}秒后重新获取`;

						// 更新剩余倒计时时间
						localStorage.setItem('countdownRemainingSeconds', this.countdownSeconds.toString());
					}
				}, 1000);
			}
		}
	}
</script>

<style lang="less">
	.signup {
		position: relative;
		width: 100%;
		height: 100vh;
		background-color: #17171f;
		overflow: hidden;

		.wave {
			position: absolute;
			top: 0;
			left: 0;
			width: 750rpx;
			height: 700rpx;
			background-color: #22222b;
			margin: 0 auto;
			overflow: hidden;
			animation: water-wave linear infinite;

			.wave1 {
				position: absolute;
				top: 15%;
				left: -20%;
				background: #1f1f29;
				width: 150%;
				height: 150%;
				border-radius: 40%;
				animation: inherit;
				animation-duration: 5s;
			}

			.wave2 {
				position: absolute;
				top: 25%;
				left: -20%;
				background: #1c1c24;
				opacity: .9;
				width: 150%;
				height: 150%;
				border-radius: 35%;
				animation: inherit;
				animation-duration: 7s;
			}

			.wave3 {
				position: absolute;
				top: 30%;
				left: -25%;
				background: #17171f;
				width: 150%;
				height: 150%;
				border-radius: 40%;
				animation: inherit;
				animation-duration: 9s;
			}

			@keyframes water-wave {
				0% {
					transform: rotate(0deg);
				}

				100% {
					transform: rotate(360deg);
				}
			}
		}

		.content {
			position: relative;
			z-index: 99;

			.close {
				width: 32rpx;
				height: 32rpx;
				margin-top: 28rpx;
			}

			.h1 {
				color: #ffffff;
				font-size: 48rpx;
				font-weight: 600;
				margin-top: 105rpx;
				margin-left: 20rpx;
				margin-bottom: 85rpx;
			}

			.form {
				margin-top: 80rpx;

				.item {
					height: 80rpx;
					display: flex;
					align-items: center;
					border-bottom: 2rpx solid #45454d;

					.phone {
						width: 32rpx;
						height: 37rpx;
					}

					.check {
						width: 32rpx;
						height: 32rpx;
					}

					.password {
						width: 30rpx;
						height: 34rpx;
					}

					.input {
						width: calc(100% - 70rpx);
						margin-left: 32rpx;
						display: flex;
						justify-content: space-between;

						input {
							font-size: 28rpx;
							color: #ffffff;
						}

						.getcheck {
							color: #1e7ef1;
						}

						.showpwd {
							width: 34rpx;
							height: 32rpx;
						}
					}
				}

				.submit {
					width: 100%;
					height: 90rpx;
					line-height: 90rpx;
					margin: 50rpx auto 40rpx auto;
					border-radius: 45rpx;
					background-color: #1e7ef1;
					color: #ffffff;
					font-size: 36rpx;
					text-align: center;
				}
			}
		}
	}
</style>