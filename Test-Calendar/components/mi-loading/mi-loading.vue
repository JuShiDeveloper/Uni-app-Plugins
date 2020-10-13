<template>
	<view class="show-loading-con" :class="{'mask': hasMask}" v-if="isShow">
		<view class="content">
			<image src="../../static/loading.gif" mode=""></image>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			hasMask: {
				type: Boolean,
				default: true,
			}
		},
		data() {
			return {
				isShow: false,
			};
		},
		methods: {
			show(){
				this.isShow = true
			},
			hide() {
				this.isShow = false
			}
		}
	}
</script>

<style lang="scss">
	.show-loading-con{
		height: 100vh;
		width: 100%;
		position: fixed;
		z-index: 999;
		top: 0;
		left: 0;
		.content{
			position: absolute;
			top: 50%;
			left: 50%;
			bottom: 0px;
			right: 0px;
			transform: translate(-50%, -50%);
			
			display: flex;
			justify-content: center;
			align-items: center;
			flex-direction: column;
			color: #fff;
			width: 3rem;
			height: 3rem;
			background: rgba(0, 0, 0, 0.7);
			border-radius: .3rem;
			image{
				width: 2rem;
				height: 2rem;
			}
		}
	}
	.mask{
		background: rgba($color: #000000, $alpha: .2);
	}
</style>
