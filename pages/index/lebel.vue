<template>
	<view class="u-wrap">
		<view class="newview1" v-for="(item,index) in tabbar" :key="index">
			<view v-if="item.name != '推荐' && item.name != '最新'&& item.name != '排行'" style="padding: 30rpx 30rpx;">
				<view class="" @click="labelclick(item.name,item.id)">
					<view class="top">
						<view class="item-title">
							<p><text >{{item.name}}</text></p>
						</view>			
					</view>
					
				</view>				
			</view>
			
			<view style="width: 100%;background-color: #f9f9f9;height: 10rpx;">
				
			</view>
		</view>
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tabbar: [],
				scrollTop: 0, //tab标题的滚动条位置
				current: 0, // 预设当前项的值
				menuHeight: 0, // 左边菜单的高度
				menuItemHeight: 0, // 左边菜单item的高度
			}
		},
		computed: {

		},
		onLoad() {
			this.labellist()
		},
		methods: {
			//标签列表
			labellist() {
				this.$u.post('/Labels/list', {})
					.then(res => {
						if (res.code == 200) {
							this.tabbar = res.data
							// console.log(res.data)
						}
					})

			},
			//选择标签
			labelclick(name, id) {
				uni.redirectTo({
					url: './addtie?name=' + name + '&id=' + id

				})
				// console.log(name,id)

			},
			getImg() {
				return Math.floor(Math.random() * 35);
			},
			// 点击左边的栏目切换
			async swichMenu(index) {
				if (index == this.current) return;
				this.current = index;
				// 如果为0，意味着尚未初始化
				if (this.menuHeight == 0 || this.menuItemHeight == 0) {
					await this.getElRect('menu-scroll-view', 'menuHeight');
					await this.getElRect('u-tab-item', 'menuItemHeight');
				}
				// 将菜单菜单活动item垂直居中
				this.scrollTop = index * this.menuItemHeight + this.menuItemHeight / 2 - this.menuHeight / 2;
			},
			// 获取一个目标元素的高度
			getElRect(elClass, dataVal) {
				new Promise((resolve, reject) => {
					const query = uni.createSelectorQuery().in(this);
					query.select('.' + elClass).fields({
						size: true
					}, res => {
						// 如果节点尚未生成，res值为null，循环调用执行
						if (!res) {
							setTimeout(() => {
								this.getElRect(elClass);
							}, 10);
							return;
						}
						this[dataVal] = res.height;
					}).exec();
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.u-wrap {
		height: calc(100vh);
		/* #ifdef H5 */
		height: calc(100vh - var(--window-top));
		/* #endif */
		display: flex;
		flex-direction: column;
	}

	.u-search-box {
		padding: 18rpx 30rpx;
	}

	.u-menu-wrap {
		flex: 1;
		display: flex;
		overflow: hidden;
	}

	.u-search-inner {
		background-color: rgb(234, 234, 234);
		border-radius: 100rpx;
		display: flex;
		align-items: center;
		padding: 10rpx 16rpx;
	}

	.u-search-text {
		font-size: 26rpx;
		color: $u-tips-color;
		margin-left: 10rpx;
	}

	.u-tab-view {
		width: 200rpx;
		height: 100%;
	}

	.u-tab-item {
		height: 110rpx;
		background: #f6f6f6;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 26rpx;
		color: #444;
		font-weight: 400;
		line-height: 1;
	}

	.u-tab-item-active {
		position: relative;
		color: #000;
		font-size: 30rpx;
		font-weight: 600;
		background: #fff;
	}

	.u-tab-item-active::before {
		content: "";
		position: absolute;
		border-left: 4px solid u-type-primary;
		height: 32rpx;
		left: 0;
		top: 39rpx;
	}

	.u-tab-view {
		height: 100%;
	}

	.right-box {
		background-color: rgb(250, 250, 250);
	}

	.page-view {
		padding: 16rpx;
	}

	.class-item {
		margin-bottom: 30rpx;
		background-color: #fff;
		padding: 16rpx;
		border-radius: 8rpx;
	}

	.item-title {
		font-size: 26rpx;
		color: $u-main-color;
		font-weight: bold;
	}

	.item-menu-name {
		font-weight: normal;
		font-size: 24rpx;
		color: $u-main-color;
	}

	.item-menu-name2 {
		width: 120rpx;
		height: 50rpx;
		color: #fff;
		background-color: #2B85E4;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 50px;
	}

	.item-container {
		display: flex;
		flex-wrap: wrap;
	}

	.thumb-box {
		// background-color: #18B566;
		width: 100%;
		height: 75px;
		display: flex;
		align-items: center;
		justify-content: space-between;
		// flex-direction: column;
		margin-top: 20rpx;

		.item-menus {
			display: flex;
			justify-content: flex-start;
			align-items: center;
			margin-right: 10rpx;
		}
	}

	.item-menu-image {
		width: 120rpx;
		height: 120rpx;
	}
</style>
