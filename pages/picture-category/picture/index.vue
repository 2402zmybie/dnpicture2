<template>
	<scroll-view enable-flex scroll-y class="picture-container" @scrolltolower="handleScrolltolower">
		<view class="item-list">
			<view class="item-wrap" v-for="(item,index) in vertical" :key="item.id">
				<image :src="item.thumb" mode="widthFix"></image>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	export default {
		mounted() {
			this.getList()
		},
		props:{
			id:String,
			order:String
		},
		data() {
			return {
				params: {
					limit:30,
					skip:0,
					order:this.order
				},
				vertical:[],
				hasMore:true
			}
		},
		watch:{
			order(newValue,oldValue) {
				console.log(`图片切换页面` + newValue)
				this.vertical = []
				this.params.skip = 0;
				this.params.order = newValue
				this.getList()
			}
		},
		methods:{
			getList() {
				console.log(this.params)
				this.$request({
					url:`http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
					data:this.params
				})
				.then((result) => {
					if(result.res.vertical.length === 0) {
						uni.showToast({
							title: '没有更多数据了',
							icon:"none"
						});
						this.hasMore =false
						return
					}
					this.vertical = [...this.vertical,...result.res.vertical]
				})
			},
			handleScrolltolower() {
				if(this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				}else {
					uni.showToast({
						title: '没有更多数据了',
						icon:"none"
					});
				}
			}
			
		}
	}
</script>

<style lang="scss" scoped>
.picture-container {
	height: calc(100vh - 36px);
}
.item-list {
	border: 1upx solid red;
	display: flex;
	flex-wrap: wrap;
	.item-wrap {
		width: 33.33%;
		border: 5upx solid #FFFFFF;
		image{
			width: 100%;
		}
	}
}

</style>
