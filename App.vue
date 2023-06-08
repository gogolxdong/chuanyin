<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="less">
	/*每个页面公共css */
	/* #ifdef H5 */
	uni-page-head {
		display: none;
	}

	/* #endif */

	body {
		font-family: Source Han Sans CN;
		color: #000000;
		background-color: #17171f;
		font-size: 26rpx;
	}

	.content {
		width: 686rpx;
		margin: 0 auto;
	}
</style>