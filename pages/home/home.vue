<template>
	<view>
		<view class="search-box">
			<my-search @click="gotosearch"></my-search>
		</view>
<!-- 		//轮播图 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in swiperList" :key="i">
				<navigator class="swiper-item" :url="'/pkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navclick(item)">
				<image :src="item.image_src" class="nav-img"></image>			
			</view>
		</view>
		<!-- 楼层 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item,i) in floorList" :key="i">
				<image :src="item.floor_title.image_src" class="floor-title" mode=""></image>
				<view class="floor-img-box">
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>
					<view class="right-img-box" >
						<navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :url="item2.url":key="i2" v-if= "i2 !==0" >
							<img :src="item2.image_src" mode="widthFix" :style="{width:item2.image_width + 'rpx'}">
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
				//轮播图的数据列表
				swiperList:[],
				navList:[],
				floorList:[]
			}
		},
		onLoad(){
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods :{
			async getSwiperList(){
				const {data:res} = await uni.$http.get(`/api/public/v1/home/swiperdata`)
				if(res.meta.status !== 200) return uni.$showMsg()
				
				this.swiperList = res.message
			},
			async getNavList(){
				const {data:res} = await uni.$http.get(`/api/public/v1/home/catitems`)
				if(res.meta.status !== 200) return uni.$showMsg()
				
				this.navList = res.message
			},
			async getFloorList(){
				const {data:res} = await uni.$http.get(`/api/public/v1/home/floordata`)
				if(res.meta.status !== 200) return uni.$showMsg()
				res.message.forEach(floor=>{
					floor.product_list.forEach(prod=>{
						prod.url = '/pkg/goods_list/goods_list?'+prod.navigator_url.split('?')[1]
					})
				})
				this.floorList = res.message
			},
			navclick(item){
				if(item.name === '分类')
				{
					uni.switchTab({
						url:'/pages/cate/cate'
					})
				}
			},
			gotosearch(){
				uni.navigateTo({
					url:'/pkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">
swiper{
	height: 330rpx;
	.swiper-item,image
	{
		height: 100%;
		width:100%;
	}
}
.nav-list{
	display: flex;
	justify-content: space-around;
	margin:15px 0;
	
	.nav-img{
		width: 128rpx;
		height: 140rpx;
	}
}
.floor-title{
	height: 60rpx;
	width: 100%;
	display: flex;
}
.right-img-box{
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around;
}
.floor-img-box{
	display: flex;
	padding-left:10rpx ;
}
//吸顶
.search-box{
	position: sticky;
	top: 0;
	z-index: 999;
}
</style>
