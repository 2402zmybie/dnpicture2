<template>
	<scroll-view class="album-container" scroll-y @scrolltolower="handleScrolltoower">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" class="album-swiper">
			<swiper-item v-for="(item,index) in banner" :key="item.id">
				<view class="swiper-item">
					<image :src="item.thumb" mode="widthFix"></image>
				</view>
			</swiper-item>
		</swiper>
		<view class="album-list">
			<view class="album-item" v-for="(item,index) in album" :key="item.id" @click="goDetail(item)">
				<view class="album-item-left">
					<image :src="item.cover" mode="aspectFill"></image>
				</view>
				<view class="album-item-right">
					<view class="item-title">{{ item.name }}</view>
					<view class="item-content">{{ item.desc }}</view>
					<view class="item-btn-wrap">
						<view class="item-btn">+ 关注</view>
					</view>
				</view>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				params: {
					limit:30,
					order:"new",
					skip:0
				},
				hasMore:true,
				banner:[],
				album:[]
			}
		},
		mounted() {
			uni.setNavigationBarTitle({
				title:"专辑"
			})
			this.getList()
		},
		methods:{
			getList() {
				this.$request({
					url:"http://157.122.54.189:9088/image/v1/wallpaper/album",
					data:this.params
				})
				.then((result)=> {
					if(this.banner.length === 0) {
							this.banner = result.res.banner
					}
				
					if(result.res.album.length === 0) {
						uni.showToast({
							icon:"none",
							title:"没有更多数据了"
						})
						this.hasMore = false
						return
					}
					this.album = [...this.album,...result.res.album]
				})
			},
			handleScrolltoower() {
				if(this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				}else {
					uni.showToast({
						icon:"none",
						title:"没有更多数据了"
					})
				}
			},
			goDetail(item) {
				console.log(item.id)
				uni.navigateTo({
					url: `/pages/detail-album/index?id=${item.id}`,
					success: res => {},
					fail: (err) => {console.log(err)},
					complete: () => {}
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
.album-container {
	height: calc(100vh - 36px);
}

.album-swiper {
	//2.3 是因为图片宽高比为 2.3, 所以动态计算swiper的高度
	height: calc(750upx / 2.3);
	image {
		height: 100%;
	}
}

.album-list {
	margin-left: 10upx;
	.album-item {
		padding: 20upx;
		border-bottom: 1upx solid #CCCCCC;
		display: flex;
		.album-item-left {
			width: 200upx;
			height: 200upx;
			flex-shrink: 0;
			image{
				width: 100%;
				height: 100%;
			}
		}
		.album-item-right {
			width: 100%;
			margin-left: 20upx;
			overflow: hidden;
			.item-title {
				font-size: 34upx;
				color: #000000;
			}
			.item-content{
				size: 30upx;
				color: #AAAAAA;
				margin: 10upx 0;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
			}
			.item-btn-wrap {
				display: flex;
				justify-content: flex-end;
				.item-btn {
					border: 1upx solid $color;
					font-size: 30upx;
					color: $color;
					padding: 10upx;
				}
				
			}
		}
	}
	
}


</style>
