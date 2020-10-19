<template>
	<view class="content">
		<view class="top-nav">
			<uni-segmented-control class="uni-segmented" :current="current" :values="items" @clickItem="onClickItem" style-type="text" active-color="#D52A7E"></uni-segmented-control>
			<view class="iconfont iconsearch"></view>
		</view>
		<view class="content">
			<view v-if="current === 0">
				<recommend></recommend>
			</view>
			<view v-if="current === 1">
				<category></category>
			</view>
			<view v-if="current === 2">
				<upnew></upnew>
			</view>
			<view v-if="current === 3">
				<album></album>
			</view>
		</view>
	</view>
</template>

<script>
	import recommend from './recommend/index.vue'
	import category from './category/index.vue'
	import upnew from './upnew/index.vue'
	import album from './album/index.vue'
	export default {
		components:{
			recommend,
			category,
			upnew,
			album
		},
		data() {
			return {
				items: ['推荐', '分类', '最新', '专辑'],
				current: 0
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
