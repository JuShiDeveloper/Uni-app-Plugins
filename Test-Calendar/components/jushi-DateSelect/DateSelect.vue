<template>
	<view class="calendar-main auto" v-show="showview">
		<view class="choose_year">
			<view class="icon" @click="chooseYears(-1)">
				<<</view> <view class="icon" @click="chooseMonth(-1)">
					<</view> <view class="date">{{year}}年{{month.toString()}}月
			</view>
			<view class="icon" @click="chooseMonth(1)">></view>
			<view class="icon" @click="chooseYears(1)">>></view>
		</view>
		<!-- <view class="lunar-detail-date">
            {{lunarDetail.lunarYear.substring(0,2)}} -【{{lunarDetail.lunarYear.substring(2,3)}}】年{{lunarDetail.lunarMonth}}月{{lunarDetail.lunarDay}}
        </view> -->
		<view class="days_area">
			<view class="day week" v-for="week in weeks" :key="week">{{week}}</view>
			<view class="day day-list" @click="chooseThisDay(day.gregorian)" v-for="(day, index) in days" :key="index" :class="getClass(day)">
				<p>{{day.gregorian}}</p>
				<span>{{day.lunar}}</span>
			</view>
		</view>
		<view style="display: flex;flex-direction: row;margin-top: 10px;">
			<button type="default" style="width: 50%;margin-right: 10px;" @click="close">取消</button>
			<button type="primary" style="width: 50%;margin-left: 10px;background-color: #ff8800;" @click="confirm">确定</button>
		</view>
	</view>
</template>

<script>
	import {
		sloarToLunar,
		getYearMonthDay
	} from "./computedLunarDate.js"
	export default {
		name: 'LunarCalendar',
		props: ['currentDate'],
		data() {
			return {
				showview: false,
				year: 0,
				month: 0,
				today: 0,
				days: [],
				weeks: ['一', '二', '三', '四', '五', '六', '日'],
				todayDate: 0
			}
		},
		props: {

		},
		mounted() {
			this.todayDate = new Date().getDate();
			if (this.currentDate) {
				const now = new Date(this.currentDate);
				this.year = now.getFullYear();
				this.month = now.getMonth() + 1;
				this.today = now.getDate();
			} else {
				const now = new Date();
				this.year = now.getFullYear();
				this.month = now.getMonth() + 1;
				this.today = now.getDate();
			}
			this.getDays()
		},
		computed: {
			selectDate() {
				return `${this.year}-${this.month.toString().padStart(2, '0')}-${this.today}`
			},
			lunarDetail() {
				var lunarDate = sloarToLunar(this.year, this.month.toString().padStart(2, '0'), this.today);

				return lunarDate;
			}
		},
		methods: {
			// 获取当前月份所有公历日期及其农历日期
			getDays() {
				this.days = [];
				const time = new Date();
				time.setFullYear(this.year, this.month, 0);
				let date = new Date(this.year, (this.month - 1), 1);
				var week = date.getDay();
				if (week == 0) {
					week = 7;
				}
				for (let i = 1; i < week; i++) {
					this.days.push({
						gregorian: '',
						lunar: ''
					})
				}
				for (let i = 1; i <= time.getDate(); i++) {
					this.days.push({
						gregorian: i,
						lunar: getYearMonthDay(this.year, this.month, i)
					})
				}
			},
			// 改变年份
			chooseYears(state) {
				this.year += state;
				if (new Date().getFullYear() === this.year && (new Date().getMonth() + 1) === this.month) {
					this.today = this.todayDate;
				} else {
					this.today = 0;
				}
				this.getDays()
			},
			// 改变月份
			chooseMonth(state) {
				this.month += state;
				if (new Date().getFullYear() === this.year && (new Date().getMonth() + 1) === this.month) {
					this.today = this.todayDate;
				} else {
					this.today = 0;
				}
				if (this.month < 1) {
					this.month = 12;
					this.chooseYears(-1)
				} else if (this.month > 12) {
					this.month = 1;
					this.chooseYears(1)
				} else {
					this.getDays()
				}
			},
			chooseThisDay(day) {
				const now = new Date();
				let year = now.getFullYear();
				let month = now.getMonth() + 1;
				let today = now.getDate();
				let date = new Date(this.year, (this.month - 1), day);
				//今天之前的天数不可选
				if (this.year == year && this.month == month && parseInt(day) < today) {
					return
				} else if (date.getDay() == 0 || date.getDay() == 6) {
					return //周末不可选
				} else if (this.year < year) {
					//当年之前的年不可选
					return
				} else if(this.year == year && this.month < month){
					//当月之前的月
					return
				}else if (this.month == 1 && day == 1) { //元旦节
					return
				} else if (this.month == 10 && day > 0 && day <= 7) {
					return //国庆节
				}
				if (day > 0) {
					this.today = day;
				}
			},
			// getBit (m, n) {
			//     return 29 + ((m >> n) & 1)
			// },
			show() {
				this.showview = true;
				this.$emit('show')
			},
			hide() {
				this.showview = false;
				this.$emit('hide')
			},
			close() {
				this.showview = false;
				this.$emit('close')
			},
			confirm() {
				this.$emit('changeDate', this.selectDate)
				this.$emit('changelunarDate', this.lunarDetail)
				this.showview = false;
			},
			getClass(day) {
				const now = new Date();
				let year = now.getFullYear();
				let month = now.getMonth() + 1;
				let today = now.getDate();
				let date = new Date(this.year, (this.month - 1), day.gregorian);
				if (day.gregorian === this.today) {
					return 'choose_day'
				} else if (this.year == year && this.month == month && day.gregorian > 0 && day.gregorian < today) {
					//今天之前的天数标记为灰色，表示不可选
					return 'unchoose_day'
				} else if (date.getDay() == 0 || date.getDay() == 6) {
					//周末不可选
					return 'unchoose_day'
				} else if (this.year < year) {
					//当年之前的年不可选
					return 'unchoose_day'
				} else if(this.year == year && this.month < month){
					//当月之前的月
					return 'unchoose_day'
				}
				else {
					return this.isHoliday(day) //判断是否节假日
				}
			},
			isHoliday(day) {
				if (this.month == 1 && day.gregorian == 1) {//元旦节
					return 'unchoose_day'
				} else if(day.lunar ==='清明节'){
					return 'unchoose_day'
				}else if(day.lunar ==='劳动节'){
					return 'unchoose_day'
				}else if(day.lunar ==='端午节'){
					return 'unchoose_day'
				}else if(day.lunar ==='中秋节'){
					return 'unchoose_day'
				}else if (this.month == 10 && day.gregorian > 0 && day.gregorian <= 7) {
					return 'unchoose_day' //国庆节
				}
				return ''
			}
		}
	}
</script>

<style lang="less" scoped>
	.calendar-main {
		width: 650rpx;
		position: absolute;
		left: 0;
		right: 0;
		z-index: 99;
		margin-top: 10rpx;
		background-color: #fff;
		box-shadow: 0 4rpx 24rpx 0 rgba(0, 0, 0, 0.2);
		-webkit-border-radius: 10rpx;
		-moz-border-radius: 10rpx;
		border-radius: 10rpx;

		.choose_year {
			display: flex;

			.icon {
				width: 10%;
				height: 70rpx;
				line-height: 70rpx;
				text-align: center;
				cursor: pointer;

				&:hover {
					color: #FF6800;
				}
			}

			.date {
				width: 60%;
				text-align: center;
				height: 70rpx;
				line-height: 70rpx;
				font-size: 32rpx;

			}
		}

		.lunar-detail-date {
			line-height: 35rpx;
			text-align: center;
			color: #FF6800;
			font-size: 30rpx;
		}

		.days_area {
			display: flex;
			flex-wrap: wrap;

			.day {
				width: 14.28%;
				line-height: 54rpx;
				text-align: center;
				cursor: pointer;

				p {
					margin: 0;
					font-size: 24rpx;
					line-height: 40rpx;
				}

				span {
					font-size: 24rpx;
				}
			}

			// .day-list {
			// 	&:hover {
			// 		background-color: #ff9800;
			// 		-webkit-border-radius: 10rpx;
			// 		-moz-border-radius: 10rpx;
			// 		border-radius: 10rpx;
			// 	}
			// }

			.week {
				background-color: #fff;
				color: #19233c;
				font-weight: bold;
				height: 80rpx;
				line-height: 80rpx;
				margin: 0;
			}

			.choose_day {
				background-color: #FF6800;
				color: #fff;
				font-weight: bold;
				-webkit-border-radius: 10rpx;
				-moz-border-radius: 10rpx;
				border-radius: 10rpx;

				span {
					color: #fff;
				}

				p {
					color: #fff;
				}
			}
		}

		.unchoose_day {
			background-color: #F8F8F8;
			color: #333;
			font-weight: bold;
			// -webkit-border-radius: 10rpx;
			// -moz-border-radius: 10rpx;
			// border-radius: 10rpx;

			span {
				color: #333;
			}

			p {
				color: #333;
			}
		}

		.bottom-btn {
			text-align: right;

			span {
				padding: 8rpx 16rpx;
				margin-right: 10rpx;
				cursor: pointer;
				border: 2rpx solid #999;
				-webkit-border-radius: 10rpx;
				-moz-border-radius: 10rpx;
				border-radius: 10rpx;

				&:hover {
					color: #FF6800;
					border: 2rpx solid #FF6800;
				}
			}
		}
	}
</style>
