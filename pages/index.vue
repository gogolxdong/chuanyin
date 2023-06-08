<template>
	<view class="gpt">
		<image src="@/static/img/chatbg.png" class="upbg"></image>
		<image src="@/static/img/chatbg.png" class="downbg"></image>
		<view class="title">
			传音对话
		</view>
		<view class="chat">
			<view class="tips" v-if="!msg.length">
				<!-- <view class="tips"> -->
				<!-- <view class="h1">GPT AI</view> -->
				<view class="item">
					<view class="h2">
						<image src="@/static/img/power.png"></image>
						能力展示
					</view>
					<view class="ammo">
						<view>
							<view>“用简单的数据解释量子计算”</view>
							<text>></text>
						</view>
						<view>
							<view>“如何用JAVASCRIPT发送HTTP请求”</view>
							<text>></text>
						</view>
						<view>
							<view>“对于影视视频有什么推荐么？”</view>
							<text>></text>
						</view>
					</view>
				</view>
				<view class="item">
					<view class="h2">
						<image src="@/static/img/gear.png"></image>
						功能展示
					</view>
					<view class="ammo">
						<view>
							<view class="center">
								记住用户之前对话
							</view>
						</view>
						<view>
							<view class="center">
								允许接受适当请求的培训
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="msg" v-for="(message, index) in msg" :key="index">
				<view class="date">
					{{ message.date }}
				</view>
				<view class="text" v-if="message.type == 1">
					<image :src="useravatar"></image>
					<view>
						{{ message.content }}
					</view>
				</view>
				<view class="audio" v-if="message.type == 2">
					<image src="@/static/img/man.png"></image>
					<view @click="playaudio(message.content)">
						{{ message.length }}″
						<image src="@/static/img/voice.png"></image>
					</view>
				</view>
				<view class="ai" v-if="message.type == 3">
					<image :src="aiavatar"></image>
					<view>{{ message.content }}</view>
				</view>
			</view>
		</view>
		<view class="input">
			<view class="pannel" v-if="this.inputway == 0">
				<image src="@/static/img/audio.png" @click="changeway"></image>
				<input placeholder="" v-model="message" @keydown.enter="sendChatMessage" />
				<button @click="sendChatMessage">发送</button>
			</view>
			<view class="pannel" v-else>
				<image src="@/static/img/keyboard.png" @click="changeway"></image>
				<view class="speak" @touchstart="startRecording" @touchmove="handleTouchMove" @touchend="stopRecording">
					按住说话
				</view>
			</view>
		</view>
		<view class="recourding" v-if="nnshow">

			<view class="isstop">
				<view class="blue" v-if="needCancel == false">
					<nnvoice />
				</view>
				<view class="touch" v-if="needCancel == false">
					<image src="@/static/img/isstop.png"></image>
					<view>上滑取消</view>
				</view>
				<view class="cancel" v-if="needCancel == true">
					<view class="red" v-if="needCancel == true">
						<nnvoice />
					</view>
					<view>松开取消</view>
					<image src="@/static/img/stop.png"></image>
				</view>
			</view>
			<view class="rcd"></view>
		</view>
	</view>
</template>

<script>
	import nnvoice from '@/components/nnvoice.vue'
	import moment from 'moment'
	// const recorderManager = uni.getRecorderManager();
	export default {
		components: {
			nnvoice
		},
		data() {
			return {
				inputway: 0,
				useravatar: '/static/img/man.png',
				aiavatar: '/static/img/logo.png',
				msg: [ //1 文字,2 语音,3 AI回复
					// {
					// 	type: 1,
					// 	content: '请描述一下祖国的大好河山，用优美的语句，精彩绝伦的展示山水湖泊已经各个城市的优美景点和人文特色 ，请不要用重复的语句来展示！',
					// 	length: '60',
					// 	date: '2020-06-24 18:00'
					// },
					// {
					// 	type: 2,
					// 	content: '请描述一下祖国的大好河山，用优美的语句，精彩绝伦的展示山水湖泊已经各个城市的优美景点和人文特色 ，请不要用重复的语句来展示！',
					// 	length: '4',
					// 	date: '2020-06-24 18:00'
					// },
					// {
					// 	type: 3,
					// 	content: '请描述一下祖国的大好河山，用优美的语句，精彩绝伦的展示山水湖泊已经各个城市的优美景点和人文特色 ，请不要用重复的语句来展示！',
					// 	length: '30',
					// 	date: '2020-06-24 18:00'
					// }
				],
				lockReconnect: false,
				websock: null,
				message: "",
				assistant: "",
				nnshow: 0,
				isRecording: false,
				needCancel: false,
				recordedBlob: null,
				mediaRecorder: null,
				startX: 0,
				startY: 0,
			}
		},
		onLoad() {

		},
		mounted() {
			const wsuri = "ws://ws.zhongshu.info/ws";
			this.websock = new WebSocket(wsuri)
			const date = moment().format('YYYY-MM-DD HH:mm:SS')
			console.log(date)
			this.websock.onmessage = (e) => {
				if (e.data === "assistant") {
					this.assistant = ""
					this.msg.push({
						type: 3,
						content: this.assistant,
						length: '0',
						date: date 
					})
				} else {
					console.log(e.data)
					this.assistant += e.data
					this.msg[this.msg.length - 1].content = this.assistant
				}
			}
			// 连接建立时触发
			this.websock.onopen = () => {
				console.log("open")
			}
			// 通信发生错误时触发
			this.websock.onerror = () => {
				console.log("error")
			}
			// 连接关闭时触发
			this.websock.onclose = (e) => {
				console.log("断开连接", e);
				var that = this;
				if (that.lockReconnect) {
					return;
				}
				that.lockReconnect = true;
				that.timeoutnum && clearTimeout(that.timeoutnum);
				that.timeoutnum = setTimeout(function() {
					that.websock = new WebSocket(wsuri)
					that.lockReconnect = false;
				}, 5000);


			}
		},
		methods: {
			changeway() {
				this.inputway = this.inputway === 0 ? 1 : 0;
			},
			playaudio(e) {
				console.log('播放音频' + e)
			},
			startRecording(e) {
				this.nnshow = 1;
				// recorderManager.start();
				this.removetabbar()
				this.length = 1;
				this.startX = e.touches[0].pageX;
				this.startY = e.touches[0].pageY;
				this.timer = setInterval(() => {
					this.length += 1;
					if (this.length >= 60) {
						clearInterval(this.timer);
						this.stopRecording()
					}
				}, 1000);
				console.log('录音开始')
				navigator.mediaDevices.getUserMedia({
						audio: true
					})
					.then(stream => {
						this.isRecording = true;
						this.recordedBlob = null;

						this.mediaRecorder = new MediaRecorder(stream);
						this.mediaRecorder.start();

						this.mediaRecorder.addEventListener('dataavailable', event => {
							if (event.data.size > 0) {
								this.recordedBlob = event.data;
							}
						});
					})
					.catch(error => {
						console.error('无法访问麦克风', error);
					});
			},
			handleTouchMove(e) {
				if (this.startY - e.touches[0].pageY > 50) {
					this.needCancel = true;
					console.log('需要停止')
				} else {
					this.needCancel = false;
					console.log('解除停止')
				}
			},
			stopRecording() {
				this.sendChatMessage()
				clearInterval(this.timer);
				// recorderManager.stop();
				if (this.mediaRecorder && this.isRecording) {
					this.mediaRecorder.stop();
					this.isRecording = false;
					if (!this.needCancel) {
						console.log('录音发送' + this.mediaRecorder)
					} else {
						console.log('录音停止')
					}

				}
				this.needCancel = false
			},
			sendVoiceMessage() {
				if (this.recordedBlob) {
					// 在这里将 recordedBlob 发送给服务器或执行其他操作
					// 例如，使用 FormData 将语音文件上传到服务器

					// 清除录音数据
					this.recordedBlob = null;
				}
			},
			removetabbar() {
				uni.hideTabBar();
			},
			sendChatMessage() {
				this.websock.send(this.message)
				this.msg.push({
					type: 1,
					content: this.message,
					length: '2',
					date: moment().format('YYYY-MM-DD HH:mm:SS')
				})
				this.message = ""
				this.nnshow = 0
				uni.showTabBar();
			}
		}
	}
</script>

<style lang="less">
	.gpt {
		position: relative;
		height: calc(100vh - 95rpx);
		overflow: hidden;

		.upbg {
			position: absolute;
			width: 650rpx;
			height: 261rpx;
			margin: 0 auto;
			left: 0;
			right: 0;
			top: 0;
		}

		.downbg {
			position: absolute;
			width: 650rpx;
			height: 261rpx;
			margin: 0 auto;
			left: 0;
			right: 0;
			bottom: 0;
			-moz-transform: scaleY(-1);
			-webkit-transform: scaleY(-1);
			-o-transform: scaleY(-1);
			transform: scaleY(-1)
		}

		.title {
			text-align: center;
			height: 88rpx;
			line-height: 88rpx;
			font-size: 36rpx;
			font-weight: 600;
			color: #ffffff;
		}

		.chat {
			box-sizing: border-box;
			width: 750rpx;
			padding: 0 32rpx;
			height: calc(100vh - 300rpx);
			margin: 0 auto;
			overflow-y: scroll;

			.tips {
				.h1 {
					font-size: 60rpx;
					font-weight: 600;
					color: #1e7ef1;
					text-align: center;
					margin: 50rpx auto;
				}

				.item {
					.h2 {
						display: flex;
						align-items: center;
						justify-content: center;
						font-size: 28rpx;
						color: #ffffff;
						padding-top: 15rpx;
						margin-bottom: 25rpx;

						image {
							width: 30rpx;
							height: 30rpx;
							margin-right: 15rpx;
						}
					}

					.ammo {
						>view {
							box-sizing: border-box;
							display: flex;
							align-items: center;
							justify-content: space-between;
							width: 100%;
							height: 100rpx;
							line-height: 100rpx;
							padding: 0 30rpx;
							background-color: #323548;
							font-size: 28rpx;
							color: #b6b7cc;
							border-radius: 10rpx;
							margin-bottom: 16rpx;

							.center {
								margin: 0 auto;
							}

							text {
								font-size: 36rpx;
								color: #1e7ef1;
							}
						}
					}
				}
			}
		}

		.msg {
			.date {
				text-align: center;
				font-size: 24rpx;
				color: #9b9aa0;
				height: 95rpx;
				line-height: 95rpx;
			}

			.text {
				margin-bottom: 40rpx;

				>image {
					width: 80rpx;
					height: 80rpx;
					border-radius: 50%;
					margin-left: 15rpx;
					float: right;
				}

				view {
					width: 500rpx;
					border-radius: 10rpx;
					box-sizing: border-box;
					color: #ffffff;
					background-color: #1e7cf2;
					padding: 25rpx;
					font-size: 28rpx;
					line-height: 38rpx;
					float: right;
				}

				&::after {
					content: "";
					display: block;
					clear: both;
				}
			}

			.audio {
				margin-bottom: 40rpx;

				>image {
					width: 80rpx;
					height: 80rpx;
					border-radius: 50%;
					margin-left: 15rpx;
					float: right;
				}

				view {
					height: 90rpx;
					padding: 0 40rpx;
					border-radius: 10rpx;
					box-sizing: border-box;
					color: #ffffff;
					background-color: #1e7cf2;
					font-size: 28rpx;
					float: right;
					display: flex;
					align-items: center;

					image {
						width: 34rpx;
						height: 47rpx;
						margin-left: 10rpx;
					}
				}

				&::after {
					content: "";
					display: block;
					clear: both;
				}
			}

			.ai {
				margin-bottom: 40rpx;

				>image {
					width: 80rpx;
					height: 80rpx;
					border-radius: 50%;
					margin-right: 15rpx;
					float: left;
				}

				view {
					width: 500rpx;
					border-radius: 10rpx;
					box-sizing: border-box;
					color: #ffffff;
					background-color: #323548;
					padding: 25rpx;
					font-size: 28rpx;
					line-height: 38rpx;
					float: left;
				}

				&::after {
					content: "";
					display: block;
					clear: both;
				}
			}
		}

		.input {
			position: absolute;
			left: 0;
			right: 0;
			bottom: 0;
			width: 100%;
			height: 110rpx;
			background-color: #141623;
			border-top: 2rpx solid #1b1d29;

			.pannel {
				width: 686rpx;
				height: 110rpx;
				margin: 0 auto;
				display: flex;
				align-items: center;
				justify-content: space-between;

				image {
					width: 56rpx;
					height: 56rpx;
					margin-right: 16rpx;
				}

				input {
					box-sizing: border-box;
					width: 480rpx;
					height: 80rpx;
					line-height: 80rpx;
					padding: 0 28rpx;
					border-radius: 40rpx;
					border: 2rpx solid #1e7ef1;
					font-size: 28rpx;
					color: #ffffff;
				}

				button {
					width: 120rpx;
					height: 80rpx;
					border-radius: 40rpx;
					font-size: 32rpx;
					color: #ffffff;
					background-color: #1e7ef1;
				}

				.speak {
					box-sizing: border-box;
					width: calc(100% - 72rpx);
					height: 80rpx;
					line-height: 80rpx;
					padding: 0 28rpx;
					border-radius: 40rpx;
					border: 2rpx solid #1e7ef1;
					font-size: 28rpx;
					color: #ffffff;
					text-align: center;
				}
			}
		}
	}

	.recourding {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		background-color: rgba(0, 0, 0, 0.8);

		.rcd {
			position: absolute;
			width: 750rpx;
			height: 274rpx;
			bottom: 0;
			background: url(@/static/img/recording.png);
			background-size: cover;
		}

		.isstop {
			width: 100%;
			height: calc(100% - 274rpx);
		}

		.blue {
			text-align: center;
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			width: 353rpx;
			height: 187rpx;
			background: url(@/static/img/blue.png);
			background-size: cover;
			box-sizing: border-box;
			padding-top: 60rpx;
		}

		.touch {
			position: absolute;
			left: 0;
			right: 0;
			margin: 0 auto;
			bottom: 300rpx;
			text-align: center;

			>image {
				width: 135rpx;
				height: 135rpx;
				margin: 0 auto;
			}

			>view {
				font-size: 28rpx;
				color: #b9b9bb;
				margin-top: 25rpx;
			}
		}

		.red {
			margin: 40rpx auto;
			text-align: center;
			width: 201rpx;
			height: 157rpx;
			background: url(@/static/img/red.png);
			background-size: cover;
			box-sizing: border-box;
			padding-top: 50rpx;
		}

		.cancel {
			position: absolute;
			left: 0;
			right: 0;
			margin: 0 auto;
			bottom: 350rpx;
			text-align: center;

			>image {
				width: 135rpx;
				height: 135rpx;
				margin: 0 auto;
				margin-top: 25rpx;
			}

			>view {
				font-size: 28rpx;
				color: #b9b9bb;
			}
		}


	}
</style>