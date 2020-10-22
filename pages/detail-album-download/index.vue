<template>
	<view>
		<view class="top-user-image">
			<view class="user">
				<image :src="imgDetail.user.avatar" mode="aspectFill"></image>
				<view class="name-time">
					<view>{{ imgDetail.user.name }}</view>
					<view>{{ imgDetail.cnTime }}</view>
				</view>
			</view>
			<!-- 高清大图 -->
			<image :src="imgDetail.thumb" mode="widthFix"></image>
			<view class="rank-collect-wrap">
				<view class="rank-wrap">
					<view class="iconfont icondianzan">{{ imgDetail.rank }}</view>
				</view>
				<view class="collect-wrap">
					<view class="iconfont iconshoucang">收藏</view>
				</view>
			</view>
		</view>
		
		<!-- 最热评论 -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment-title">
				<text class="iconfont iconhot"></text> 
				<text class="comment-test">最热评论</text>
			</view>
			<!-- 评论列表 -->
			<view class="comment-list">
				<view class="comment-item" v-for="(item,index) in hot" :key="item.id">
					<view class="comment-user">
						<view class="user-left">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<view class="user-right">
							<view class="user-name">{{ item.user.name }}</view>
							<view class="user-time">{{ item.cnTime }}</view>
						</view>
						<view class="user-badge">
							<image 
								mode="widthFix"
								v-for="item2 in item.user.title"
								:key="item2.icon"
								:src="item2.icon" 
								></image>
						</view>
					</view>
					<view class="comment-desc-wrap">
						<view class="comment-desc">{{ item.content }}</view>
						<view class="iconfont icondianzan">{{ item.size }}</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最新评论 -->
		<view class="comment new" v-if="comment.length">
			<view class="comment-title">
				<text class="iconfont iconpinglun"></text> 
				<text class="comment-test">最新评论</text>
			</view>
			<!-- 评论列表 -->
			<view class="comment-list">
				<view class="comment-item" v-for="(item,index) in comment" :key="item.id">
					<view class="comment-user">
						<view class="user-left">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<view class="user-right">
							<view class="user-name">{{ item.user.name }}</view>
							<view class="user-time">{{ item.cnTime }}</view>
						</view>
						<view class="user-badge">
							<image 
								mode="widthFix"
								v-for="item2 in item.user.title"
								:key="item2.icon"
								:src="item2.icon" 
								></image>
						</view>
					</view>
					<view class="comment-desc-wrap">
						<view class="comment-desc">{{ item.content }}</view>
						<view class="iconfont icondianzan">{{ item.size }}</view>
					</view>
				</view>
			</view>
		</view>
		
		<view class="down-btn-wrap">
			<view class="down-btn" @click="download()">下载图片</view>
		</view>
	</view>
</template>

<script>
	import moment from '../../utils/moment.js'
	export default {
		onLoad() {
			console.log(getApp().globalData.list)
			console.log("下载图片索引" + getApp().globalData.index)
			const {index} = getApp().globalData;
			this.imgIndex = index
			this.getData()
		},
		data() {
			return {
				imgList:[],
				//图片信息对象 包含着用户头像等
				imgDetail: {},
				album:[],
				hot:[],
				comment:[],
				//图片的索引
				imgIndex:0
			}
		},
		methods: {
			getData() {
				//解构得到数组
				const {list} = getApp().globalData
				this.imgList = list
				this.imgDetail = this.imgList[this.imgIndex]
				//xxx年前的数据(时间格式化)
				this.imgDetail.cnTime = moment(this.imgDetail.atime*1000).fromNow();
				// console.log(JSON.stringify(this.imgDetail))
				
				//获取评论数据
				this.getComments(this.imgDetail.id)
			},
			getComments(id) {
				const url = `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`;
				this.$request({
					url:url
				})
				.then((result)=> {
					this.album = result.res.album
					//给hot数组中的对象添加一个时间属性 xxx月前
					result.res.hot.forEach(v=> {
						v.cnTime = moment(v.atime*1000).fromNow()
					})
					this.hot = result.res.hot;
					//给comment数组中的对象添加一个时间属性 xxx月前
					result.res.comment.forEach(v => {
						v.cnTime=moment(v.atime*1000).fromNow()
					})
					this.comment = result.res.comment
					console.log(`最热评论条数:${this.hot.length}`)
					console.log(`最新评论条数:${this.comment.length}`)
					console.log(this.comment)
				})
			},
			async download() {
				await uni.showLoading({
					title:"下载中"
				})
				//1 将远程文件下载到小程序内存中 tempFilePath
				const result1 = await uni.downloadFile({url:this.imgDetail.img})
				const {tempFilePath} = result1[1];
				//2 将小程序内存中的临时文件下载到本地上
				const result2 = uni.saveImageToPhotosAlbum({filePath:tempFilePath})
				// console.log(result2)
				// console.log("下载成功")
				uni.hideLoading();
				await uni.showToast({
					title:"下载成功"
				})
			}
			
			
		}
	}
</script>

<style lang="scss" scoped>
.top-user-image {
	.user {
		display: flex;
		padding: 20upx 40upx;
		image {
			width: 70upx;
			height: 80upx;
		}
		.name-time {
			margin-left: 20upx;
			display: flex;
			flex-direction: column;
			justify-content: space-between;
			view:nth-child(1) {
				color: #000000;
				font-weight: 600;
				font-size: 36upx;
			}
			view:nth-child(2) {
				color: #CCCCCC;
				font-size: 24upx;
			}
			
		}
	}
	image {
		
	}
	.rank-collect-wrap {
		border-bottom: 1upx solid #CCCCCC;
		height: 80upx;
		width: 100%;
		display: flex;
		.rank-wrap,.collect-wrap {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			color: #CCCCCC;
		}
	}
}

.comment {
	&.new {
		color: green;
	}
	&.hot {
		color: red;
	}
	.comment-title  {
		padding: 20upx;
		.comment-test {
			margin-left: 8upx;
			font-size: 26upx;
			color: #AAAAAA;
		}
	}
	
	.comment-list {
		margin-top: 4upx;
		.comment-item {
			padding: 20upx;
			border-bottom: 4upx solid #CCCCCC;
			.comment-user {
				display: flex;
				width: 100%;
				.user-left {
					width: 10%;
					display: flex;
					flex-shrink: 0;
					image{
						width: 80%;
					}
				}
				.user-right{
					display: flex;
					flex: 1;
					flex-direction: column;
					justify-content: space-between;
					.user-name{
						font-size: 26upx;
						color: #000000;
					}
					.user-time{
						font-size: 22upx;
						color: #CCCCCC;
					}
				}
				.user-badge {
					display: flex;
					padding: 0 20upx;
					image {
						display: inline-block;
						width: 30upx;
					}
				}
			}
			.comment-desc-wrap {
				margin-left: 10%;
				padding: 20upx 0;
				display: flex;
				flex-direction: row;
				justify-content: space-between;
				.comment-desc  {
					color: #666666;
					font-size: 30upx;
				}
				.icondianzan {
					padding: 0 20upx;
					color: #D9E1FA;
					font-size: 26upx;
				}
			}
		}
	}	
}

.down-btn-wrap {
	height: 80upx;
	width: 100%;
	background: #FFFFFF;
	position: fixed;
	bottom: 0;
	left: 0;
	display: flex;
	justify-content: center;
	align-items: center;
	.down-btn {
		width: 90%;
		height: 80%;
		background-color: $color;
		font-size: 30upx;
		font-weight: 600;
		color: #FFFFFF;
		display: flex;
		justify-content: center;
		align-items: center;
	}
}
</style>
