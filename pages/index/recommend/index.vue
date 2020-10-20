<template>
	<scroll-view scroll-y class="recommend-container" @scrolltolower="handleScrolltolower">
		<view class="recommend-list">
			<view class="recommend-wrap" v-for="item in recommends">
				<image :src="item.thumb" mode="widthFix"></image>
			</view>
		</view>
		
		<view class="month-wrap">
			<view class="month-left">
				<view class="month-DD">{{ months.DD }}/</view>
				<view class="month-MM">{{ months.MM }} 月</view>
				<view class="month-title">{{ months.title }}</view>
			</view>
			<view class="month-right">
				更多 >
			</view>
		</view>
		<view class="month-list-wrap">
			<view class="month-item-wrap" v-for="(item,index) in months.items" :key="index">
				<image :src="item.img + item.rule.replace('$<Height>',360)" mode="aspectFill"></image>
			</view>
		</view>
		
		<view class="hots-wrap">
			<view class="hots-title">热门</view>
			<view class="hots-list-wrap">
				<view class="hots-item-wrap" v-for="(item,index) in hots" :key="item.id">
					<image :src="item.thumb" mode="widthFix"></image>
				</view>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	import moment from '../../../utils/moment.js'
	export default {
		data() {
			return {
				recommends: [],
				months:{},
				hots:[],
				// 后期要修改的参数 提取出来 放在data中
				params:{
					limit:30,
					order:"hot",
					skip:0
				},
				hasMore:true
			}
		},
		//组件挂载的时候请求
		mounted() {
			uni.setNavigationBarTitle({
				title:"推荐"
			})
			this.getList()
		},
		methods:{
			getList() {
				this.$request({
					url:"http://157.122.54.189:9088/image/v3/homepage/vertical",
					data:this.params
				})
				.then((result) => {
					if(this.recommends.length == 0) {
						this.recommends = result.res.homepage[1].items;
						this.months = result.res.homepage[2];
						this.months.MM = moment(this.months.stime).format("MM");
						this.months.DD = moment(this.months.stime).format("DD");
					}
					
					if(result.res.vertical.length ===0) {
						this.hasMore = false
						uni.showToast({
							icon:"none",
							title:"没有更多数据了"
						})
						return
					}
					this.hots = [...this.hots,...result.res.vertical]
				})
			},
			handleScrolltolower() {
				if(this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				}else {
					uni.showToast({
						icon:"none",
						title:"没有更多数据了"
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	
.recommend-container {
	height: calc(100vh - 36px);
}	
	
// flex-wrap: wrap;超出换行
.recommend-list {
	display: flex;
	flex-wrap: wrap;
	.recommend-wrap {
		width: 50%;
		border: 5upx solid #FFFFFF;
	}
}

.month-wrap {
	display: flex;
	justify-content: space-between;
	padding: 20upx;
	.month-left {
		display: flex;
		align-items: center;
		font-size: 30upx;
		color: $color;
		font-weight: 600;
		.month-MM {
			font-size: 22upx;
		}
		.month-title {
			color: #9E9E9E;
			margin-left: 20upx;
		}
	}
	.month-right{
		display: flex;
		align-items: center;
		color: $color;
		font-size: 22upx;
	}
}
.month-list-wrap {
	display: flex;
	flex-wrap: wrap;
	.month-item-wrap {
		width: 33.33%;
		border: 5upx solid #FFFFFF;
	}
}

.hots-wrap {
	padding: 20upx;
	.hots-title {
		border-left: 6upx solid $color;
		padding-left: 20upx;
		margin-bottom: 20upx;
		font-weight: 600;
	}
	.hots-list-wrap {
		display: flex;
		flex-wrap: wrap;
		.hots-item-wrap {
			width: 33.33%;
			border: 5upx solid #FFFFFF;
		}
	}
}

</style>
