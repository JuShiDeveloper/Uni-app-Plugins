<template>
	<view class="content">
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="取消" @clickbutton="click(true)" textColor="#333333"></jushi-button>
			<view style="width: 1px;background-color: #F1F1F1;height: 50px;"></view>
			<jushi-button buttonText="选择日期" @clickbutton="click(false)" textColor="#09cc9f"></jushi-button>
		</view>
		<view>
			<date-select ref="dateselect" @changeDate="changeDate" @changelunarDate="changelunarDate"></date-select>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="loading弹窗" @clickbutton="loading()" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="顶部弹窗" @clickbutton="showDialog('top')" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="中间弹窗" @clickbutton="showDialog('center')" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="底部弹窗" @clickbutton="showDialog('bottom')" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="消息提示弹窗" @clickbutton="showDialog('message')" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="对话框弹窗" @clickbutton="showDialog('dialog')" textColor="#09cc9f"></jushi-button>
		</view>
		<view style="display: flex;flex-direction: row;width: 100%;">
			<jushi-button buttonText="分享弹窗" @clickbutton="showDialog('share')" textColor="#09cc9f"></jushi-button>
		</view>
		<!-- 加载动画 -->
		<mi-loading ref="Loading" title=""></mi-loading>
		<!-- 弹出弹窗 -->
		<uni-popup ref="popupDialog" :type="type" maskClick="true" >
			<view style="display: flex;flex-direction: row;align-items: center;justify-content: center;background-color: #FFFFFF;">
				<view style="margin: 40px;">
					<text>我是从{{type}}弹出的！</text>
				</view>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	import DateSelect from "../../components/jushi-DateSelect/DateSelect.vue";
	import miLoading from '../../components/mi-loading/mi-loading.vue';
	export default {
		components: {
			DateSelect,
			miLoading
		},
		data() {
			return {
				type: 'top',
			}
		},
		onLoad() {

		},
		methods: {
			click(isCancel){
				if(isCancel){
					this.$refs.dateselect.close();
				}else{
					//显示日历选择器
					// console.log(this.$refs.dateselect);
					this.$refs.dateselect.show();
				}
			},
			changeDate(datestr) {
				//返回的是阳历 数字的  比如:2020-02-02
				console.log(datestr);
			},
			changelunarDate(lunardate) {
				//返回的是对象 具体对象内容看图片或者自己看控制台输出
				console.log(lunardate);
			},
			loading(){
				this.$refs.Loading.show();
				uni.showToast({
					icon:'none',
					title:'3秒后取消'
				})
				setTimeout(()=>{
					this.$refs.Loading.hide();
				},3000)
			},
			showDialog(type){
				this.type = type;
				this.$refs.popupDialog.open();
				
				setTimeout(()=>{
					this.$refs.popupDialog.close();
				},3000)
			}
			
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
