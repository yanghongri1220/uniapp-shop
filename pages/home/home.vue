<template>
	<view>
		<!-- 轮播区 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<swiper-item v-for="item,i in swiperlist" :key="i">
				<navigator :url="'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id" class="swiper-item">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航区 -->
		<view class="nav-box">
			<view @click="navJump(item)" class="nav-item" v-for="item,i in navList" :key="i">
				<image class="nav-image" :src="item.image_src" mode=""></image>
			</view>
		</view>
		<!-- 楼层区 -->
		<view class="floor-box">
			<view class="floor-item" v-for="item,i in floorList" :key="i">
				<view class="floor-title">
					<image :src="item.floor_title.image_src" mode=""></image>
				</view>
				<view class="floor-content">
					<navigator class="content-left" :url="item.product_list[0].url">
						<image :style="{width:item.product_list[0].image_width+'rpx'}"
							:src="item.product_list[0].image_src" mode="widthFix"></image>
					</navigator>
					<view class="content-right">
						<navigator v-for="item1,i1 in item.product_list" :url="item1.url" class="" v-if="
							i1!=0" :key="i1">
							<image v-if="i1!=0" :style="{width:item1.image_width+'rpx'}" :src="item1.image_src"
								mode="widthFix">
							</image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperlist: [],
				navList: [],
				floorList: [],
			};
		},
		methods: {
			async getSwiperList() {
				const res = await uni.$http.get('/api/public/v1/home/swiperdata')
				this.swiperlist = res.data.message
			},
			async getNavList() {
				const res = await uni.$http.get('/api/public/v1/home/catitems')
				this.navList = res.data.message
			},
			async getFloorList() {
				const res = await uni.$http.get('/api/public/v1/home/floordata')
				this.floorList = res.data.message
				this.floorList.map(item => {
					item.product_list.map(item1 => {
						item1.url = '/subpkg/goods_list/goods_list?' + item1.navigator_url.split("?")[1]
					})
				})
				console.log("楼层区域", res.data.message)
			},
			navJump(item) {
				if (item.name == '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			}
		},
		onLoad() {
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		}
	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;

		.swiper-item,
		image {
			width: 100%;
			height: 100%;
		}
	}

	.nav-box {
		display: flex;
		margin: 10rpx;
		justify-content: space-between;

		.nav-image {
			width: 128rpx;
			height: 140rpx;
		}
	}

	.floor-box {
		.floor-item {
			.floor-title {
				margin-top: 15rpx;

				image {
					width: 100%;
					height: 60rpx;
				}
			}

			.floor-content {
				display: flex;

				.content-right {
					margin-left: 10rpx;
					display: flex;
					flex-wrap: wrap;
					justify-content: space-around;
				}
			}
		}


	}
</style>