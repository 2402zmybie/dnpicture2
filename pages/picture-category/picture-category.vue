<template>
	<view class="content">
		<view class="top-nav">
			<uni-segmented-control class="uni-segmented" :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#D52A7E"></uni-segmented-control>
			<view class="iconfont iconsearch"></view>
		</view>
		<view class="content">
			<view v-if="current === 0">
				<pictureIndex :id="id" :order="items[0].order"></pictureIndex>
			</view>
			<view v-if="current === 1">
				<pictureIndex :id="id" :order="items[1].order"></pictureIndex>
			</view>
		</view>
	</view>
</template>	

<script>
	import pictureIndex from './picture/index.vue'
	export default {
		onLoad(options) {
			this.id = options.id
		},
		components:{
			pictureIndex
		},
		data() {
			return {
				items: [
					{title: '最新',order:'new'},
					{title: '热门',order:'hot'}
				],
				current: 0,
				id:0
			}
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
.top-nav {
	position: relative;
	.uni-segmented {
		width: 60%;
		margin: 0 auto;
	}
	.iconsearch {
		position: absolute;
		right: 20upx;
		top: 50%;
		transform: translateY(-50%);
	}
}

</style>
