<template>
	<view class="myboss">
		<view class="mytop">
			<view class="top">
				<view class="topleft">
					<view class="topleftimg">
						<u-avatar src="../static/uview/example/my1.png" v-if="!userInfod"></u-avatar>
						<u-avatar :src="userInfod.logo" size="120" v-if="userInfod"></u-avatar>
					</view>
					<view class="topleftname">
						<button class="topright" open-type="getUserProfile" lang="zh_CN" @tap="wxGetUserInfo"
							v-if="!userInfod">立即登录</button>
						<p v-if="userInfod">{{userInfod.name}}</p>

						<p>{{userInfod.sign}}</p>
					</view>
				</view>
				<view class="topright2" v-if="userInfod" @click="updatelist">
					编辑资料
				</view>
			</view>
			<view class="bottomview1" v-if="!userInfod">
				<view class="">
					<text>0</text>
				</view>
				<view class="">
					<text>0</text>
				</view>
				<view class="">
					<text>0</text>
				</view>
			</view>
			<view class="bottomview1" v-else>
				<view class="">
					<text v-if='tiexglist.tiezi'>{{tiexglist.tiezi}}</text>
					<text v-else>0</text>
				</view>
				<view class="">
					<text v-if='tiexglist.pl'>{{tiexglist.pl}}</text>
					<text v-else>0</text>
				</view>
				<view class="">
					<text v-if='tiexglist.dz'>{{tiexglist.dz}}</text>
					<text v-else>0</text>
				</view>
			</view>
			<view class="bottomview">
				<view class="">
					<text>帖子</text>
				</view>
				<view class="">
					<text>评论</text>
				</view>
				<view class="">
					<text>获赞</text>
				</view>

			</view>


		</view>
		<!-- 内容 -->
		<view class="my_content">
			<view class="" slot="body">
				<view class="my_list" @click="mytieclick">
					<u-icon name="account" color="#333" size="40"></u-icon>
					<span>我的帖子</span>
					<view class="mylisticon">
						<u-icon name="arrow-right" color="#333" size="30"></u-icon>
					</view>
				</view>
				<view class="my_list" @click="myscclick">
					<u-icon name="file-text" color="#333" size="40"></u-icon>
					<span>我的收藏</span>
					<view class="mylisticon">
						<u-icon name="arrow-right" color="#333" size="30"></u-icon>
					</view>
				</view>

				<view class="my_list" @click="myjlclick">
					<u-icon name="info-circle" color="#333" size="40"></u-icon>
					<span>阅读历史</span>
					<view class="mylisticon">
						<u-icon name="arrow-right" color="#333" size="30"></u-icon>
					</view>
				</view>
				<view class="my_list" @click="myplclick">
					<u-icon name="info-circle" color="#333" size="40"></u-icon>
					<span>我的评论</span>
					<view class="mylisticon">
						<u-icon name="arrow-right" color="#333" size="30"></u-icon>
					</view>
				</view>
				<view class="my_list" v-if="userInfod" @click="loginout">
					<u-icon name="info-circle" color="#333" size="40"></u-icon>
					<span>退出登录</span>
					<view class="mylisticon">
						<u-icon name="arrow-right" color="#333" size="30"></u-icon>
					</view>
				</view>
			</view>
		</view>
		<!-- 弹出 -->
		<u-popup v-model="show" mode="center">
			<view class="popupview">

			</view>
		</u-popup>
	</view>
</template>

<script>
	import WXBizDataCrypt from '../../common/WXBizDataCrypt.js'
	import app from '../../App.vue'
	export default {
		data() {
			return {
				show: false,
				xcxcode: '', //获取登录用户的code
				userInfo: {
					avatarUrl: '',
					city: '',
					country: '',
					gender: 1,
					province: '',
					nickName: ''
				},
				userInfod: uni.getStorageSync("userInfo"),
				code: "",
				SessionKey: '',
				encryptedData: "",
				iv: "",
				OpenId: '',
				nickName: null,
				avatarUrl: null,
				telephone: '',
				tiexglist: {}


			}
		},
		onLoad() {
			this.login();
			this.tiexgdata()
			// console.log(uni.getStorageSync("userInfo"))
		},
		onShow() {
			this.tiexgdata()
		},
		methods: {
			//我的帖子相关信息
			tiexgdata() {
				this.$u.post('/Forum/userdata', {
						uid: this.userInfod.id
					})
					.then(res => {
						if (res.code == 200) {
							this.tiexglist = res.data
						}

					})

			},
			//修改信息
			updatelist() {
				uni.redirectTo({
					url: './updatexx'
				})

			},
			//我的帖子
			mytieclick() {
				if (this.userInfod) {
					uni.navigateTo({
						url: './mytie'
					})
				} else {
					uni.showToast({
						title: '请先登录',
						icon: 'none'
					});
				}

			},
			//我的收藏
			myscclick() {
				if (this.userInfod) {
					uni.navigateTo({
						url: './mysoucang'
					})
				} else {
					uni.showToast({
						title: '请先登录',
						icon: 'none'
					});
				}

			},
			//阅读历史
			myjlclick() {
				if (this.userInfod) {
					uni.navigateTo({
						url: './myjl'
					})
				} else {
					uni.showToast({
						title: '请先登录',
						icon: 'none'
					});
				}

			},
			//我的评论
			myplclick() {
				if (this.userInfod) {
					uni.navigateTo({
						url: './mypl'
					})
				} else {
					uni.showToast({
						title: '请先登录',
						icon: 'none'
					});
				}
			
			},
			//退出
			loginout() {
				let _self = this;
				app.userInfo = null
				_self.userInfo = null
				uni.removeStorageSync("userInfo")
				app.user = null
				this.tiexgdata()
				//跳转本页面
				uni.reLaunch({
					url: '../my/myindex'
				});
			
			},
			//获取用户信息
			wxGetUserInfo() {
				let _self = this;
				uni.getUserProfile({
					provider: 'weixin',
					desc: '获取用户完整信息',
					success: (infoRes) => {
						// console.log(infoRes.userInfo)
						_self.userInfo = infoRes.userInfo
						// 存入全局用户信息
						app.userInfo = infoRes.userInfo
						//提示
						uni.showLoading({
							title: '加载中',
							mask: true
						});
						// 2.提交数据到后台
						this.$u.post('User/getOpenid', {
								"code": _self.xcxcode,
								"name": _self.userInfo.nickName,
								"logo": _self.userInfo.avatarUrl,
								"sex": _self.userInfo.gender
							})
							.then(res => {
								//将数据存储到全局
								app.user = res.data.name
								uni.setStorageSync("userInfo", res.data)
								//关闭提示信息
								setTimeout(function() {
									uni.hideLoading();
								}, 1000);
								//刷新页面
								_self.userInfod = uni.getStorageSync("userInfo") //获取登录数据
								this.tiexgdata()
								//跳转本页面
								uni.switchTab({
									url: '../my/myindex'
								});
							})
					},
					fail(res) {
						uni.showToast({
							title: '获取用户信息失败',
							icon: 'none'
						});
					}
				});

				return false
			},
			//登录
			login() {
				let _self = this;
				// 1. wx 获取登录用户 code
				uni.login({
					provider: 'weixin',
					success: loginRes => {
						_self.xcxcode = loginRes.code;
						// console.log(loginRes)
					},
					fail: () => {
						return false;
					}
				});
				return false;
			},
		
			getPhoneNumber(e) { //向后台更新信息	
				let _this = this;
				let key = uni.getStorageSync("userInfo").aname;
				// console.log("key",key)
				let pc = new WXBizDataCrypt("wx5eb9d9c25e369442", key);
				// console.log("pc",pc)
				let data = pc.decryptData(e.detail.encryptedData, e.detail.iv);
				// console.log("data",data)
				uni.showLoading({
					title: '绑定中',
					mask: true
				})
				_this.$u.post('User/update', {
						'telephone': data.phoneNumber,
						'id': uni.getStorageSync("userInfo").id
					})
					.then(res => {
						//关闭提示信息
						setTimeout(function() {
							uni.hideLoading();
						}, 1000);
						if (res.code == 200) {
							uni.showToast({
								title: '绑定成功',
								icon: 'none'
							});
							// console.log(res)
							uni.setStorageSync("userInfo", res.data)
							_this.userInfod = uni.getStorageSync("userInfo")
						}

					})




			}

		}
	}
</script>

<style lang="scss" scoped>
	.myboss {
		width: 100%;

		.mytop {
			width: 100%;
			height: 400rpx;
			background-image: linear-gradient(#8FBFFC, #5B9CF9, );

			padding: 30rpx;

			.top {
				display: flex;
				justify-content: space-between;
			}

			.bottomview1 {
				width: 100%;
				height: 60rpx;
				margin-top: 100rpx;
				// background-color: #18B566;
				display: flex;
				justify-content: center;
				align-items: center;

				view {
					text-align: center;
					width: 30%;
					color: #fff;
				}

				view:last-child {
					border-right: none;
				}
			}

			.bottomview {
				width: 100%;
				height: 60rpx;
				// background-color: #18B566;
				display: flex;
				justify-content: center;
				align-items: center;

				view {
					text-align: center;
					width: 30%;
					border-right: 1px solid #fff;
					color: #fff;
				}

				view:last-child {
					border-right: none;
				}
			}

		}

		.topleft {
			height: 100rpx;
			color: #fff;

			.topleftimg {
				float: left;
				margin-right: 30rpx;
			}

			.topleftname {
				float: left;
				line-height: 50rpx;

				p {
					line-height: 60rpx;
					font-size: 16px;

				}

				p:last-child {
					font-size: 11px;
				}
			}

			// display: flex;
			// justify-content:flex-start ;
			// align-items: center;
			// .topleftname{
			// 	display: flex;
			// 	flex-direction:row-reverse;
			// }
		}

		.topright {
			// width: 170rpx;
			height: 67rpx;
			color: #fff !important;
			font-size: 12px;
			border: 1px solid #fff;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 50px;
			background-color: rgba($color: #000000, $alpha: 0) !important;
			margin-top: 10rpx;
		}

		.topright2 {
			width: 170rpx;
			height: 67rpx;
			color: #fff !important;
			font-size: 12px;
			border: 1px solid #fff;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 50px;
			margin-top: 20rpx;
		}

		.u-card-wrap {
			background-color: $u-bg-color;
			padding: 1px;
		}

		.u-body-item {
			font-size: 32rpx;
			color: #333;
			padding: 20rpx 10rpx;
		}

		.u-body-item image {
			width: 120rpx;
			flex: 0 0 120rpx;
			height: 120rpx;
			border-radius: 8rpx;
			margin-left: 12rpx;
		}

		.my_top {
			width: 100%;
			background-color: #333333;
			height: 250rpx;
			position: relative;
		}

		.my_top1>image {
			width: 100rpx;
			height: 100rpx;
			border-radius: 50%;
			margin-top: 30rpx;
			margin-left: 40rpx;
			float: left;
		}

		.my_top_p {
			float: left;
			margin-top: 50rpx;
			margin-left: 40rpx;
		}

		.my_top_p .p1 {
			font-size: 35rpx;
			color: #fff;
		}

		.my_top_p .p2 {
			margin-top: 15rpx;
			font-size: 25rpx;
			color: #fff;
		}

		.my_top_p2 {
			background-color: #FFFFFF;
			position: absolute;
			width: 90%;
			margin-left: 5%;
			height: 150rpx;
			bottom: -60rpx;
			color: #000;
			border-radius: 10rpx;
			padding-top: 30rpx;
			box-shadow: 0px 0px 5px #e6e6e6 !important;
		}

		.my_top_p2 p {
			margin-top: 10rpx;
		}

		.demo-layout {
			text-align: center;
		}

		.my_content {
			padding: 40rpx 30rpx;
			// width: 84%;
			// margin-left: 8%;
			// margin-bottom: 100rpx;
			// margin-top: 30rpx;

		}


		.my_list {
			width: 100%;
			height: 100rpx;
			line-height: 100rpx;
			color: #333;
			border-bottom: 1px solid #f5f0f0bf;
		}

		.my_list span {
			margin-left: 20rpx;
			font-size: 30rpx;
		}

		.mylisticon {
			float: right;
		}

		.my_list button {
			float: left !important;
			background-color: #fff !important;
			font-size: 30rpx !important;
			color: #333 !important;
			padding-left: 20rpx !important;
			width: 70% !important;
			height: 90rpx;
			line-height: 90rpx;
			text-align: left !important;

		}

		.my_list button::after {
			border: none !important;
		}

		.kf_icon {
			float: left;
			margin-top: 31rpx;
		}

		.mylist2 {
			margin-bottom: 40rpx;
		}


	}

	.popupview {
		padding: 10px;
	}

	.getCaptcha {
		background-color: #18B566;
		color: #fff;
		border: none;
		font-size: 30rpx;
		padding: 0rpx 14rpx;
		margin-top: 10rpx;


		&::after {
			border: none;
		}
	}
</style>
