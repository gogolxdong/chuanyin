<template>
	<view class="init">
		<image src="@/static/img/logo1.png" class="logo1"></image>
		<image src="@/static/img/logo2.png" class="logo2"></image>
		<view class="animate-wave">
			<view class="w1"></view>
			<view class="w2"></view>
			<view class="w3"></view>
			<view class="w4"></view>
			<view class="w5"></view>
			<view class="w6"></view>
		</view>
		<view class="menu">
			<text class="h1">欢迎来到传音</text>
			<text class="h2">加入我们使用更多智能服务，获取更多<br>有趣经历！</text>
			<navigator url="/pages/login" class="start">
				立即使用
			</navigator>
		</view>
	</view>
</template>

<script>
</script>

<style lang="less">
	.init {
		position: relative;
		width: 100%;
		height: 100vh;
		background-color: #191b2c;
		overflow: hidden;

		.logo1 {
			top: 272rpx;
			left: 0;
			right: 0;
			margin: auto;
			position: absolute;
			width: 273rpx;
			height: 274rpx;
			z-index: 99;
		}

		.logo2 {
			position: absolute;
			top: 400rpx;
			left: 370rpx;
			width: 27rpx;
			height: 27rpx;
			animation: circle 2s linear infinite;
			z-index: 99;
		}

		@keyframes circle {
			0% {
				transform: rotate(-40deg) translate(128rpx) rotate(40deg);
			}

			// 50% {
			// 	transform: rotate(320deg) translate(128rpx) rotate(-320deg);
			// }

			100% {
				transform: rotate(320deg) translate(128rpx) rotate(-320deg);
			}
		}

		.animate-wave {
			width: 2500rpx;
			height: 2500rpx;
			position: absolute;
			top: -695rpx;
			left: -920rpx;
			right: 0;
			margin: 0 auto;
		}

		@-webkit-keyframes opac {
			from {
				opacity: 1;
				width: 100rpx;
				height: 100rpx;
				top: 1050rpx;
				left: 1250rpx;
			}

			to {
				opacity: 0;
				width: 100%;
				height: 100%;
				top: 0;
				left: 0;
			}
		}

		.animate-wave {
			* {
				background: #1b1d2d;
				position: absolute;
				border-radius: 50%;
				animation: opac 5s infinite;
				border: 2rpx solid;
				border-color: #d1d1d1;
			}

			.w2 {
				animation-delay: .1s;
			}

			.w3 {
				animation-delay: .4s;
			}

			.w4 {
				animation-delay: .7s;
			}

			.w5 {
				animation-delay: 1s;
			}

			.w6 {
				animation-delay: 1.3s;
			}
		}

		.menu {
			bottom: 120rpx;
			left: 0;
			right: 0;
			margin: auto;
			position: absolute;
			z-index: 99;
			text-align: center;

			.h1 {
				font-size: 48rpx;
				font-weight: 600;
				color: #ffffff;
				display: block;
				margin-bottom: 25rpx;
			}

			.h2 {
				display: block;
				font-size: 30rpx;
				line-height: 48rpx;
				color: #d1d2d3;
				margin-bottom: 70rpx;
			}

			.start {
				width: 416rpx;
				height: 92rpx;
				line-height: 92rpx;
				margin: 0 auto;
				border-radius: 50rpx;
				background-color: #1e7cf2;
				color: #ffffff;
				font-size: 30rpx;
			}
		}
	}
</style>