<template>
	<view>
		下载图片
	</view>
</template>

<script>
	export default {
		onLoad() {
			console.log(getApp().globalData.list)
			console.log("下载图片索引" + getApp().globalData.index)
		},
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
