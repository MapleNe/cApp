<template>
	<view>
		<z-paging ref="paging" @query="getData" v-model="article">
			<template #top>
				<u-navbar title="我的收藏" placeholder autoBack>
					<view slot="left">
						<i class="ess icon-left_line" style="font-size: 60rpx;"></i>
					</view>
				</u-navbar>
			</template>
			<block v-for="(item,index) in article" :key="index">
				<view style="margin: 30rpx;" @tap.stop="item.type=='post'?goArticle(item):item.type=='photo'?goPhoto(item):goArticle(item)">
					<article-header :data="item"></article-header>
					<article-photo :data="item" v-if="item.type=='photo'"></article-photo>
					<article-content :data="item" v-else></article-content>
					<article-footer :data="item"></article-footer>
				</view>
				<view style="border-bottom: 1rpx solid #f7f7f7;"></view>
			</block>
		</z-paging>
	</view>
</template>

<script>
	import articleHeader from '@/components/article/header.vue';
	import articleContent from '@/components/article/content.vue';
	import articleFooter from '@/components/article/footer.vue';
	import articlePhoto from '@/components/article/photo.vue';
	export default {
		components: {
			articleHeader,
			articleContent,
			articleFooter,
			articlePhoto
		},
		data() {
			return {
				article: []
			};
		},
		methods: {
			getData(page, limit) {
				this.$http.post('/article/markList', {
					page,
					limit,

				}).then(res => {
					console.log(res)
					if (res.data.code == 200) {
						this.$refs.paging.complete(res.data.data.data)
					}
				})
			},
			goArticle(data) {
				uni.setStorageSync(`article_${data.cid}`, data)
				this.$Router.push({
					path: '/pages/article/article',
					query: {
						id: data.cid
					}
				})
			},
			goPhoto(data) {
				this.$Router.push({
					path: '/pages/article/photo',
					query: {
						id: data.cid
					}
				})
			},
		}
	}
</script>

<style lang="scss">

</style>