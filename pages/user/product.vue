<template>
	<view>
		<z-paging @query="getList" ref="paging" v-model="data">
			<template #top>
				<u-navbar title="我的小摊" autoBack placeholder>
					<view slot="left" style="display:flex;align-items: center;">
						<i class="ess icon-left_line" style="font-size: 60rpx;"></i>
					</view>
					<i class="ess icon-add_line" slot="right" style="font-size: 40rpx;" @click="goEdit()"></i>
				</u-navbar>
				<view style="padding: 20rpx;background: white;">
					<u-row>
						<view style="margin-right: 20rpx;">
							<u-button @click="showSelect = true" style="height: 60rpx;">{{select.name}}</u-button>
						</view>
						<u-search></u-search>
					</u-row>
				</view>
			</template>
			<view style="margin: 20rpx;">
				<block v-for="(item,index) in data" :key="index">
					<view style="background: white;padding: 30rpx;border-radius: 20rpx;margin-bottom: 20rpx;"
						@click.stop="goProduct(item.id)">
						<u-row align="top">
							<image :src="item.imgurl && item.imgurl[0]" mode="aspectFill"
								style="width: 120rpx;height: 120rpx;border-radius: 20rpx;background: #f7f7f7;"></image>
							<view style="margin-left: 20rpx;">
								<view style="display: flex;flex-direction: column;">
									<text style="font-weight: 600;">{{item.title}}</text>
									<text style="color: #999;font-size: 30rpx">规格数量：{{item.specs.length}}</text>
									<u-row style="margin-top: 10rpx;flex-wrap: wrap;">
										<block v-for="(specs,subIndex) in item.specs" :key="subIndex">
											<view
												style="background: #f7f7f7;border-radius: 50rpx;padding: 8rpx 12rpx;font-size: 26rpx;">
												<text>{{specs.name}}</text>
											</view>
										</block>
									</u-row>
								</view>
							</view>
						</u-row>
						<u-row style="margin-top: 20rpx;">
							<view @click.stop="goEdit(1,item.id)">
								<u-button style="height: 60rpx;" shape="circle">修改</u-button>
							</view>
						</u-row>
					</view>
				</block>
			</view>
		</z-paging>
		<u-picker :show="showSelect" keyName="name" :columns="columns" closeOnClickOverlay @close="showSelect = false"
			@confirm="select = $event.value[0];$refs.paging.reload();showSelect = false"></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tabIndex: 0,
				columns: [
					[{
						name: '全部',
					}]
				],
				data: [],

				showSelect: false,
				select: {
					name: '全部'
				}
			};
		},
		created() {
			this.getData()
		},
		methods: {
			getData() {
				this.$http.post('/shop/typeList', {
					limit: 30,
				}).then(res => {
					if (res.data.code) {
						let data = res.data.data.data
						data.forEach(item => {
							this.columns[0].push({
								name: item.name,
								id: item.id
							});
						});
					}
				})
			},
			getList(page, limit) {
				this.$http.post('/shop/list', {
					page,
					limit,
					params: JSON.stringify({
						uid: this.$store.state.userInfo.uid,
						sort: this.select.id ? this.select.id : ''
					})
				}).then(res => {
					console.log(res)
					if (res.data.code==200) {
						this.$refs.paging.complete(res.data.data.data)
					}
				}).catch(err => {
					this.$refs.paging.complete(false)
				})
			},
			con(e) {
				console.log(e)
			},
			goProduct(id) {
				this.$Router.push({
					path: '/pages/shop/product',
					query: {
						id
					}
				})
			},
			goEdit(update, id) {
				this.$Router.push({
					path: '/pages/shop/addProduct',
					query: {
						update,
						id
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	page {
		background: #f7f7f7;
	}
</style>