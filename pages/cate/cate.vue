<template>
	<view>
	<my-search @click="gotoSearch"></my-search>
		<view class="scroll-view-container">
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh+'px'}" >
				<block v-for="(item,i) in cateList" :key="i" >
					<view :class="['left-scroll-view-item',i==active? 'active':'']" @click="activeChange(i)">
					{{item.cat_name}}
					</view>
				</block>
			</scroll-view>
			<scroll-view class="right-scroll-view" scroll-y="true" :style="{height: wh+'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item2,i2) in cateLevel2" :key="i2">
					<view class="cate-lv2-title">
						/{{item2.cat_name}}/
						<view class="cate-lv3-list">
								<view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotogoodslist(item3)">
									<image :src="item3.cat_icon"></image>
									<text>{{item3.cat_name}}</text>
								</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh:0,
				cateList:[],
				active:0,
				cateLevel2:[],
				scrollTop:0
			};
		},
		onLoad() {
			const systeminfo = uni.getSystemInfoSync()
			this.wh=systeminfo.windowHeight -50
			this.getCateList()
		},
		methods:{
			async getCateList(){
				const {data:res} = await uni.$http.get('/api/public/v1/categories')
				if(res.meta.status !== 200) return uni.$showMsg()
				this.cateList=res.message
				this.cateLevel2=res.message[0].children
			},
			activeChange(i){
				this.active = i
				this.cateLevel2 = this.cateList[i].children
				this.scrollTop=this.scrollTop === 0? 1:0
			},
			gotogoodslist(item3){
				uni.navigateTo({
					url:'/pkg/goods_list/goods_list?cid=' + item3.cat_id
				})
			},
			gotoSearch(){
				uni.navigateTo({
					url:'/pkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">

.scroll-view-container{
	display: flex;
}
.left-scroll-view{
	width: 120px;
}
.left-scroll-view-item{
	background-color: #F7F7F7;
	line-height: 60px;
	text-align: center;
	font-size: 12px;
	
	&.active{
		background-color: #FFFFFF;
		position: relative;
		&::before{
			content: '';
			display: block;
			width: 3px;
			height: 30px;
			background-color: #C00000;
			position: absolute;
			top: 50%;
			left: 0;
			transform: translateY(-50%);
		}
	}
}
.cate-lv2-title{
	font-size: 12px;
	font-weight: bold;
	text-align: center;
	padding: 15px 0;
}
.cate-lv3-list{
	display: flex;
	flex-wrap: wrap;
	.cate-lv3-item{
		width: 33.33%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		margin-bottom: 10px;
		image{
			width: 60px;
			height: 60px;
		}
		text{
			font-size: 12px;
		}
	}
}
</style>
