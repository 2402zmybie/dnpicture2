<template>
	<view @click="toDetail()">
		<slot></slot>
	</view>
</template>

<script>
	export default {
		props:{
			list:Array,
			index:Number
		},
		methods:{
			toDetail() {
				console.log(111)
				getApp().globalData.list = this.list;
				getApp().globalData.index = this.index;
				uni.navigateTo({
					url: '/pages/detail-album-download/index',
					success: res => {},
					fail: (err) => {
						console.log(err)
					},
					complete: () => {}
				});
			}
		}
	}
</script>

<style>
</style>
