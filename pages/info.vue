<template>
	<view class="signup">
		<image src="@/static/img/info.png" class="infoboy"></image>
		<view class="content">
			<view class="h1">
				完善个人信息
			</view>
			<view class="h2">
				我们会保护您的信息安全
			</view>
			<view class="infoform">
				<view class="avatar">
					<image :src="src" @tap="upload"></image>
					<image src="@/static/img/camera.png" class="camera" @tap="upload"></image>
				</view>
				<view class="realform">
					<view class="item">
						<text>您的姓名</text>
						<view>
							<input maxlength="11" placeholder="请设置"
								placeholder-style="font-size:30rpx;color:#b6b7cc;" />
						</view>
					</view>
					<view class="item">
						<text>性别</text>
						<radio-group>
							<label>
								<radio value="male" checked="true" />男
							</label>
							<label>
								<radio value="female" />女
							</label>
						</radio-group>
					</view>
					<view class="item">
						<text>身高</text>
						<view>
							<picker @change="bindPickerChange" :value="height" :range="this.heightlist"
								placeholder="请选择">
								<view v-if="height">{{heightlist[height]}}</view>
								<view class="tip" v-else>请选择></view>
							</picker>
						</view>
					</view>
					<view class="item">
						<text>生日</text>
						<view>
							<picker mode="date" :value="birth" :start="startDate" :end="endDate"
								@change="bindDateChange">
								<view v-if="birth">{{birth}}</view>
								<view class="tip" v-else>请选择></view>
							</picker>
						</view>
					</view>
					<view class="item">
						<text>职业</text>
						<view>
							<view>
								<input maxlength="20" placeholder="请设置"
									placeholder-style="font-size:30rpx;color:#b6b7cc;" />
							</view>
						</view>
					</view>
					<view class="item">
						<text>城市</text>
						<view>
							<pick-regions :defaultRegion="defaultRegionCode" @getRegion="handleGetRegion">
								<view v-if="regionName">{{regionName}}</view>
								<view class="tip" v-else>请选择></view>
							</pick-regions>
						</view>
					</view>
				</view>
			</view>
			<view class="submit" @click="skip">
				提交信息
			</view>
			<view class="skip" @click="skip">
				跳过
			</view>
		</view>
	</view>
</template>

<script>
	import pickRegions from '@/components/pick-regions/pick-regions.vue'
	export default {
		components: {
			pickRegions
		},
		data() {
			return {
				src: '/static/img/man.png',
				height: 80,
				heightlist: [],
				birth: null,
				job: null,
				jobs: ['前端', '后端', '产品', '设计', '运营', '其他'],
				region: [],
				defaultRegion: ['广东省', '广州市', '番禺区'],
				defaultRegionCode: '440113'
			}
		},
		onLoad() {},
		created() {
			this.generateArray();
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			},
			regionName() {
				// 转为字符串
				return this.region.map(item => item.name).join(' ')
			}
		},
		methods: {
			upload() {
				uni.chooseImage({
					count: 1, // 默认9
					sizeType: ['compressed'], // 可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
					success(res) {
						this.src = res.tempFilePaths[0];
						console.log(this.src)
						// const src = res.tempFilePaths[0];

						// uni.redirectTo({
						// 	url: '../upload/upload?src=' + src
						// });
					}
				});
			},
			generateArray() {
				let array = [];
				for (let i = 100; i <= 200; i++) {
					array.push(i + 'cm');
				}
				this.heightlist = array;
				console.log(this.heightlist); // 打印数组到控制台
			},
			bindPickerChange: function(e) {
				this.height = e.detail.value
			},
			bindDateChange: function(e) {
				this.birth = e.detail.value
			},
			bindJobChange: function(e) {
				this.job = e.detail.value
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			handleGetRegion(region) {
				this.region = region
			},
			skip() {
				uni.switchTab({
					url: '/pages/member'
				});
			}
		},
		onLoad(option) {
			let {
				avatar
			} = option;
			if (avatar) {
				this.src = avatar;
			}
		}
	}
</script>

<style lang="less">
	.signup {
		position: relative;
		width: 100%;
		min-height: 100vh;
		background-color: #17171f;

		.infoboy {
			position: absolute;
			top: 0;
			right: 0;
			width: 407rpx;
			height: 378rpx;
		}

		.content {
			position: relative;
			z-index: 99;

			.h1 {
				color: #ffffff;
				font-size: 48rpx;
				font-weight: 600;
				padding-top: 135rpx;
				margin-left: 20rpx;
				margin-bottom: 25rpx;
			}

			.h2 {
				color: #b6b7cc;
				font-size: 26rpx;
				margin-left: 20rpx;
				margin-bottom: 85rpx;
			}

			.infoform {
				border-radius: 18rpx;
				background-color: rgb(50, 53, 72);
				box-shadow: 0.2rpx 10px 51rpx 0 rgba(30, 124, 242, 0.13);
				padding: 70rpx 42rpx;

				.avatar {
					position: relative;
					width: 163rpx;
					height: 163rpx;
					border-radius: 50%;
					border: 4rpx solid #b3b5bc;
					margin: 0 auto;

					image {
						width: 100%;
						height: 100%;
					}

					.camera {
						position: absolute;
						bottom: 0;
						right: 0;
						width: 36rpx;
						height: 36rpx;
					}
				}

				.realform {
					margin-top: 60rpx;

					.item {
						box-sizing: border-box;
						width: 100%;
						height: 96rpx;
						padding: 0 25rpx;
						display: flex;
						justify-content: space-between;
						align-items: center;
						background-color: #484b5c;
						border: 2rpx solid #303d5b;
						margin-top: 36rpx;
						font-size: 30rpx;
						color: #fff;

						>view {
							width: calc(100% - 150rpx);
							text-align: right;
						}

						label {
							margin-left: 20rpx;
						}

						input {
							font-size: 30rpx;
							color: #fff;
							text-align: right;
							height: 96rpx;
							line-height: 96rpx;
						}

						picker {
							float: right;
						}

						.tip {
							color: #b6b7cc;
						}
					}
				}
			}

			.submit {
				width: 100%;
				height: 90rpx;
				line-height: 90rpx;
				margin: 60rpx auto 25rpx auto;
				border-radius: 45rpx;
				background-color: #1e7ef1;
				color: #ffffff;
				font-size: 36rpx;
				text-align: center;
			}

			.skip {
				font-size: 28rpx;
				color: #b6b7cc;
				text-align: center;
				padding-bottom: 140rpx;
			}
		}
	}
</style>