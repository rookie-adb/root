<template>
	<view>
		<view class="search-box">
			<uni-search-bar :radius="100" @confirm="search" @input="input" cancelButton="none"></uni-search-bar>
		</view>
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
				<view class="goods-name">
					{{item.goods_name}}
				</view>
			</view>
			
		</view>
		<view class="history-box">
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="clean"></uni-icons>
			</view>
			<view class="history-list">
				<uni-tag :text="item" v-for="(item,i) in historier" :key="i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
				kw:'',
				searchResults:[],
				historyList:['a','app','apple']
			};
		},
		methods:{
			input(e){
				clearTimeout(this.timer)
				this.timer = setTimeout(() => {
					this.kw = e
					this.getsearchlist()
				},500)
				
			},
			async getsearchlist(){
				if(this.kw.length === 0){
					this.searchResults = []
					return
				}
				const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
				if(res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				this.saveSearchHistory()
			},
			gotoDetail(goods_id){
				uni.navigateTo({
					url:'/pkg/goods_detail/goods_detail?goods_id='+goods_id
				})
			},
			saveSearchHistory(){
				//this.historyList.push(this.kw)
				const set = new Set(this.historyList)
				set.delete(this.kw)
				set.add(this.kw)
				this.historyList = Array.from(set)
				uni.setStorageSync('kw',JSON.stringify(this.historyList))
			},
			clean(){
				this.historyList = []
				uni.setStorageSync('kw','[]')
			},
			gotoGoodsList(kw){
				uni.navigateTo({
					url:'/pkg/goods_list/goods_list?query'+kw
				})
			}
		},
		computed:{
			historier(){
				return [...this.historyList].reverse()
			}
		},
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		}
		
	}
</script>

<style lang="scss">
.search-box{
	position: sticky;
	top: 0;
	z-index: 999;
}
.sugg-list{
	padding:0 5px;
	.sugg-item{
		font-size: 12px;
		padding: 13px 0;
		border-bottom: 1px solid #efefef;
		display: flex;
		align-items: center;
		justify-content: space-between;
		.goods-name{
			//文字不允许换行
			white-space: nowrap;
			//溢出隐藏
			overflow: hidden;
			//溢出后 用...代替
			text-overflow: ellipsis;
			margin-right: 3px;
		}
	}
}
.history-box{
	padding: 0 5px;
	.history-title{
		display: flex;
		justify-content: space-between;
		height: 40px;
		align-items: center;
		font-size: 13px;
		border-bottom: 1px solid #efefef;
	}
	.history-list{
		display: flex;
		flex-wrap: wrap;
		.uni-tag{
			margin-top: 5px;
			margin-right: 5px;
			background-color: #efefef;
			color: black;
		}
	}
}
</style>
