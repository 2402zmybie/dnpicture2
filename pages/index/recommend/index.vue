<template>
	<view>
		推荐
	</view>
</template>

<script>
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
					this.recommends = result.res.homepage[1].items;
					console.log(this.recommends.length)
					this.months = result.res.homepage[2];
					console.log(this.months)
					this.hots = result.res.vertical
					console.log(this.hots.length)
				})
			}
		}
	}
</script>

<style>
</style>
