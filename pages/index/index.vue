<template>
	<view class="circleboss">
		<u-search margin="10rpx 30rpx" @search="search" :showAction="false" placeholder="请输入搜索内容" v-model="keyword"
			:clearabled="true">
		</u-search>
		<u-tabs :list="list" :is-scroll="false" :current="current" @change="change"></u-tabs>
		<circlenew :listdata="listdata" :userinfo="userinfo"></circlenew>
	</view>
</template>

<script>
	import circlenew from './new'


	export default {
		data() {
			return {
				list: [{
					name: '推荐'
				}, {
					name: '最新'
				}, {
					name: '排行'
				}],
				current: 0,
				listdata: [],
				page: 1,
				limit: 10,
				lx: 1,
				datas: [],
				userinfo: '',
				keyword: '',
				totalPages: 0
			}
		},
		components: {

			circlenew

		},

		onShow() {
			this.userinfo = uni.getStorageSync('userInfo')
		},

		onLoad() {
			console.log("onLoad")
			this.page = 1
			this.listdata = []
			this.tielist()
		},

		methods: {

			tielist() {

				console.log("/Forum/list")
				this.$u.post('/Forum/list', {
						title: this.keyword,
						lx: this.lx,
						page: this.page,
						limit: this.limit,
						labelsid: 0,
						uid: uni.getStorageSync("userInfo").id
					})
					.then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.data) {
								this.datas = res.data.content
								this.listdata = this.listdata.concat(res.data.content)
								this.totalPages = res.data.totalPages
							}
						}
					})
			},

			change(index) {
				this.lx = index + 1
				this.listdata = []
				this.page = 1
				this.tielist()
				this.current = index;
			},

			search(keyword) {
				this.lx = 1
				this.listdata = []
				this.page = 1
				this.tielist()
				this.current = 0;
				this.keyword = ''
			}

		},
		//滚动到底部
		onReachBottom() {
			if (this.datas.length > 0 && this.totalPages > this.page) {
				this.page = this.page + 1
				this.tielist()
			}

		},
		//下拉刷新
		onPullDownRefresh() {
			this.listdata = []
			this.page = 1
			// console.log(this.lx)
			this.tielist()

		},
	}
</script>

<style lang="scss" scoped>
	.circleboss {
		width: 100%;
	}
</style>
