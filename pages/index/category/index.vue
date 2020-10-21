<template>
	<scroll-view scroll-y class="category-container">
		<view class="list-wrap">
			<view class="item-wrap" v-for="(item,index) in list" :key="item.id" @click="handleClick(item.id)">
				<image :src="item.cover" mode="widthFix"></image>
				<text class="item-category">{{ item.rname }}</text>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				list: []
			}
		},
		mounted() {
			uni.setNavigationBarTitle({
				title:"分类"
			})
			this.getList()
		},
		methods:{
			getList() {
				this.$request({
					url:'http://157.122.54.189:9088/image/v1/vertical/category'
				})
				.then((result) => {
					this.list = result.res.category
					console.log(this.list.length)
				})
			},
			handleClick(id) {
				uni.navigateTo({
					url: `/pages/picture-category/picture-category?id=${id}`
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
.category-container {
	height: calc(100vh - 36px);
	.list-wrap {
		display: flex;
		flex-wrap: wrap;
		.item-wrap {
			position: relative;
			width: 33.33%;
			border: 5upx solid #FFFFFF;
			image {
				width: 100%;
			}
			.item-category {
				position: absolute;
				bottom: 0;
				left: 20upx;
				color: #FFFFFF;
				font-size: 30upx;
			}
		}
	}
}
</style>
