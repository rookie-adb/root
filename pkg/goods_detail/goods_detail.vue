<template>
	<view v-if="goods_info.goods_name">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in goods_info.pics" :key="i">
				<image :src="item.pics_big" @click="preview(i)"></image>
			</swiper-item>
		</swiper>
		<view class="goods-info-box">
			<view class="price">
				￥{{goods_info.goods_price}}
			</view>
			<view class="goods-info-body">
				<view class="goods-name">
					{{goods_info.goods_name}}
				</view>
				<view class="favi">
					<uni-icons type="star" size="18" color="gray"></uni-icons>
					<text>收藏</text>
				</view>
			</view>
				<view class="yf">
					快递：免运费
				</view>
		</view>
		<rich-text :nodes="goods_info.goods_introduce"></rich-text>
		<view class="goods_nav">
			<uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick"></uni-goods-nav>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				goods_info:{},
				options:[{
					icon:'shop',
					text:'店铺'
				},
				{
					icon:'cart',
					text:'购物车',
					info:2
				}],
				buttonGroup:[{
					text:'加入购物车',
					backgroundColor:'#ff0000',
					color:'#fff'
				}]
			};
		},
		onLoad(options) {
			const goods_id = options.goods_id
			this.getGoodsDetail(goods_id)
		},
		methods:{
			async getGoodsDetail(goods_id){
				const {data:res} = await uni.$http.get('/api/public/v1/goods/detail',{goods_id})
				if(res.meta.status !== 200) return uni.$showMsg()
				res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="isplay:block;"').replace(/webp/g, 'jpg')
				this.goods_info = res.message
			},
			preview(i){
				uni.previewImage({
					current:i,
					urls:this.goods_info.pics.map(x => x.pics_big) //map循环
				})
			},
			onClick(e){
				if(e.content.text === '购物车'){
					uni.switchTab({
						url:'/pages/cart/cart'
					})
				}
			}
		}
	}
</script>

<style lang="scss">
swiper{
	height: 750rpx;
	image{
		width: 100%;
		height: 100%;
	}
}
.goods-info-box{
	padding: 10px;
	padding-right: 0;
	.price{
		color: #C00000;
		font-size: 18px;
		margin: 10px 0;
	}
}
.goods-info-body{
	display: flex;
	justify-content: space-between;
	.goods-name{
		font-size: 13px;
		margin-right: 10px;
	}
	.favi{
	width: 120px;
	font-size: 12px;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	border-left: 1px solid #efefef;
	color: gray;
	}
}

.yf{
	margin: 10px 0;
	font-size: 12px;
	color: gray;
}
.goods-detail-container{
	padding-bottom: 50px;
	
}
.goods_nav{
	position: fixed;
	bottom: 0;
	left: 0;
	width: 100%;
}
</style>
