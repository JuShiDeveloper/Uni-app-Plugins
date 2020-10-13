<template>
	<view class="content">
		<view :class="confirmBtnTouch ?`dialog-button-touch`:`dialog-button`" 
		:style="{color: textColor,borderBottomLeftRadius:`${bottomLeftRadius}px`,borderBottomRightRadius:`${bottomRightRadius}px`}"
		 @touchstart="confirmTouchStart" @touchend="confirmTouchEnd" @click="clickbutton()">{{buttonText}}</view>
	</view>
</template>

<script>
	export default {
		props: {
			buttonText: {
				type: String,
				default: ""
			},
			textColor: {
				type: String,
				default: "#333333"
			},
			bottomLeftRadius: {
				type: String,
				default: "0"
			},
			bottomRightRadius: {
				type: String,
				default: "0"
			}
		},
		data() {
			return {
				confirmBtnTouch: false
			}
		},
		methods: {
			confirmTouchStart() {
				this.confirmBtnTouch = true;
			},
			confirmTouchEnd() {
				this.confirmBtnTouch = false;
			},
			clickbutton() {
				this.$emit('clickbutton');
			}
		}
	};
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		width: 100%;
		height: 50px;
	}

	.dialog-button {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: row;
		color: #333333;
		font-size: 16px;
		justify-content: center;
		align-items: center;
	}

	.dialog-button-touch {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: row;
		color: #333333;
		background-color: #F1F1F1;
		font-size: 16px;
		justify-content: center;
		align-items: center;
	}
</style>
