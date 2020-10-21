<template>
	<view v-if="Object.keys(this.album).length !== 0">
		<view class="album-img-wrap">
			<image :src="album.cover" mode="widthFix"></image>
			<view class="album-img-title-wrap">
				<view class="album-img-title">{{ album.name }}</view>
				<view class="album-img-btn">关注专辑</view>
			</view>
		</view>
		
		<view class="album-middle-wrap">
			<view class="user-wrap">
				<image class="user-img" :src="album.user.avatar" mode="aspectFill"></image>
				<view class="user-name">{{ album.user.name }}</view>
			</view>
			<view class="album-desc-wrap">
				<text class="album-desc">{{ album.desc }}</text>
			</view>
		</view>
		
		<view class="album-img-list">
			<view class="album-img-wrap" v-for="(item,index) in wallpaper" :key="item.id">
				<go-detail :list="wallpaper" :index="index">
					<image :src="item.thumb + item.rule.replace('$<Height>',360)" mode="widthFix"></image>
				</go-detail>
			</view>
		</view>
	</view>
</template>

<script>
	import goDetail from '../../components/go-detail.vue'
	export default {
		components:{
			goDetail
		},
		onLoad(options) {
			console.log(options.id)
			uni.setNavigationBarTitle({
				title:"专辑详情"
			})
			this.id = options.id
			this.getList()
		},
		data() {
			return {
				params: {
					limit:30,
					order:"new",
					skip:0,
					first:1
				},
				id:-1,
				album:{},
				wallpaper:[],
				hasMore:true
			}
		},
		methods: {
			getList() {
				this.$request({
					url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
					data:this.params
				})
				.then((result)=> {
					console.log(result.res)
					if(Object.keys(this.album).length === 0) {
						//album是个空对象
						this.album = result.res.album
					}
					if(result.res.wallpaper.length === 0) {
						uni.showToast({
							title: '没有更多数据了',
							icon:"none"
						});
						this.hasMore = false
						return
					}
					this.wallpaper = [...this.wallpaper,...result.res.wallpaper]
				})
			}
		},
		onReachBottom() {
			//页面触底 触发加载更多事件
			if(this.hasMore) {
				this.params.first = 0;
				this.params.skip += this.params.limit;
				this.getList()
			}else {
				uni.showToast({
					title: '没有更多数据了',
					icon:"none"
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
.album-img-wrap {
	position: relative;
	.album-img-title-wrap {
		position: absolute;
		left: 0;
		bottom: 0;
		display: flex;
		justify-content: space-between;
		width: 100%;
		padding: 16upx;
		.album-img-title {
			font-size: 32upx;
			font-weight: 600;
			color: #FFFFFF;
		}
		.album-img-btn {
			font-size: 24upx;
			font-weight: 600;
			color: #FFFFFF;
			padding: 10upx;
			background-color: #D52A7E;
		}
	}
}

.album-middle-wrap {
	padding: 20upx;
	.user-wrap {
		display: flex;
		.user-img {
			width: 50upx;
			height: 60upx;
		}
		.user-name {
			font-size: 28upx;
			color: #000000;
			font-weight: 600;
			margin-left: 20upx;
		}
	}
	.album-desc-wrap {
		margin-top: 20upx;
		.album-desc {
			font-size: 24upx;
			color: #666666;
		}
	}
}

.album-img-list {
	display: flex;
	flex-wrap: wrap;
	.album-img-wrap {
		width: 33.33%;
		border: 4upx solid #FFFFFF;
	}
}
</style>
