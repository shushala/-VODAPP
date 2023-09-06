<template>
	<view>
		
		<media-list v-for="(item,index) in list" :key="index" :item="item" :index="index"></media-list>
		
		<!-- 默认提示 -->
		<view v-if="list.length === 0" class="flex align-center justify-center text-secondary" style="height: 200rpx;">暂无数据</view>
		<!-- 上拉加载更多 -->
		<view  v-else-if="list.length > 10"  class="flex align-center justify-center font-md text-secondary" style="height: 80rpx;" hover-class="bg-light">
			{{ loadText }}
		</view>
		
	</view>
</template>

<script>
	import mediaList from '@/components/common/media-list.vue';
	export default {
		components: {
			mediaList
		},
		data() {
			return {
				keyword:'',
				page:1,
				list:[],
				loadText:"上拉加载更多"
			}
		},
		onNavigationBarSearchInputChanged(e) {
			this.keyword = e.text
		},
		onNavigationBarButtonTap() {
			if(this.keyword === ''){
				return uni.showToast({
					title: '关键词不能为空',
					icon: 'none'
				});
			}
			this.page = 1
			this.search()
		},
		onReachBottom() {
			if(this.loadText !== '上拉加载更多'){
				return
			}
			this.loadText = '正在加载中...'
			this.page = this.page + 1
			this.search()
		},
		methods: {
			search(){
				let page = this.page
				this.$H.get(`/video_search/${this.page}?keyword=${this.keyword}`).then(res=>{
					this.list = page === 1 ? res : [...this.list,...res]
					this.loadText = res.length === 10 ? '上拉加载更多' : '没有更多了'
				}).catch(err=>{
					if(page > 1){
						this.page--
						this.loadText = '上拉加载更多'
					}
				})
			}
		}
	}
</script>

<style>

</style>
