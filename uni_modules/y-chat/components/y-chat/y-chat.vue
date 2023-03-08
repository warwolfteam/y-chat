<template>
	<view class="y-wrap"
		:style="'--bottom-height:' + bottomHeight">
		<scroll-view class="y-wrap_message_content"
			scroll-y
			:scroll-into-view="scrollToId"
			:refresher-enabled="useRefresh"
			:refresher-threshold="100"
			:refresher-triggered="triggered"
			scroll-with-animation
			refresher-default-style="white"
			refresher-background="#F2F2F2"
			@refresherrefresh="onRefresh"
			@refresherrestore="onReset"
			@tap="closeFooter">
			<view v-for="(item, index) in list"
				:key="index"
				:id="'y-chat-' + item[defaultOptions['msgId']]"
				:class="['y-wrap_message_content_box', { 'y-wrap_message_content_my' : item[defaultOptions['userId']] == userId }]">
				<view style="text-align: center; padding: 10rpx 0 20rpx;"
					v-if="item.showTime">
					{{ item.timeLabel }}
				</view>
				<view>
					<u-image width="100rpx"
						height="100rpx"
						:src="item[defaultOptions['avator']] || errorIcon"
						radius="10rpx"
						bgColor="red"></u-image>
					<view
						:class="['y-wrap_message_content_box_msg', { 'y-wrap_message_content_box_my' : item[defaultOptions['userId']] == userId }]">
						<view
							:class="['y-wrap_message_content_box_msg__name', { 'y-wrap_message_content_box_msg__my' : item[defaultOptions['userId']] == userId }]">
							<u-tag v-if="tagOptions[item[defaultOptions['tagLabel']]]"
								:bgColor="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].bgColor : ''"
								:borderColor="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].bgColor : ''"
								:color="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].color : ''"
								:text="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].text : ''"
								size="mini"></u-tag>
							<!-- <text>{{ item[defaultOptions['name']] }}</text> -->
							<text>{{ item[defaultOptions['name']] }}</text>
						</view>
						<view
							:class="['y-wrap_message_content_box__val', { 'y-wrap_message_content_box__my' : item[defaultOptions['userId']] == userId }]">
							<view :style="{ textAlign: item[defaultOptions['userId']] == userId ? 'right' : 'left' }"
								v-if="item[defaultOptions['message']]">{{ item[defaultOptions['message']] }}</view>
							<u-image @tap="lookImg(item[defaultOptions['img']])"
								v-if="item[defaultOptions['img']]"
								:src="item[defaultOptions['img']]"
								width="40vw"
								height="auto"
								mode="widthFix"></u-image>
						</view>
					</view>
					<!-- <view class="y_message_content_box_read">
						{{item[defaultOptions['read']]?'已读':'未读'}}
					</view> -->
				</view>
			</view>
		</scroll-view>
		<view class="y-wrap_footer">
			<view class="y-wrap_footer_show_box"
				id="show_box">
				<u-icon custom-style="padding: 0 10rpx"
					:size="iconSize"
					name="mic"></u-icon>
				<view class="y-wrap_footer_show_box__ipt">
					<u-input v-model="sendVal"></u-input>
				</view>
				<view>
					<!-- <u-icon custom-style="padding: 0 10rpx" :size="iconSize" name="star"></u-icon> -->
					<!-- #ifndef MP-WEIXIN -->
					<u-icon customStyle="padding: 0 10rpx;"
						:size="iconSize"
						name="plus"
						@click="showHideBox"
						:class="!sendVal ? 'width_to_mini' : 'fade_show'"
						v-if="!sendVal"></u-icon>
					<button style="width:116rpx; 60rpx; padding: 0; text-align: center; margin-left: 10rpx;"
						type="primary"
						size="mini"
						:class="sendVal ? 'width_to_large' : 'fade_show'"
						@click="send"
						v-else>发送</button>
					<!-- #endif -->
					<!-- #ifdef MP-WEIXIN -->
					<u-transition :show="!sendVal">
						<u-icon customStyle="padding: 0 10rpx;"
							:size="iconSize"
							name="plus"
							@click="showHideBox"
							v-if="!sendVal"></u-icon>
					</u-transition>
					<u-transition :show="sendVal">
						<button style="width:116rpx; 60rpx; padding: 0; text-align: center; margin-left: 10rpx;"
							type="primary"
							size="mini"
							v-if="sendVal"
							@click="send">发送</button>
					</u-transition>
					<!-- #endif -->
				</view>
			</view>
			<view class="y-wrap_footer_hide_box"
				id="hide_box">
				<view class="y-wrap_footer_hide_box_item"
					v-for="(item, index) in sheet"
					:key="index"
					@click="moreFun(item)">
					<view class="y-wrap_footer_hide_box_item__btn">
						<u-image v-if="item.img"
							:src="item.img"
							width="60rpx"
							height="60rpx"
							mode="aspectFill" />
						<u-icon v-else
							:size="iconSize"
							:name="item.icon"></u-icon>
						<text>{{ item.name }}</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
	function disposeTime(time) {
		const nowDate = new Date().getTime()
		const cha = nowDate - time
		const oneDay = 24 * 60 * 60 * 1000
		const timeDate = new Date(time)
		const timeYear = timeDate.getFullYear()
		const timeMonth = timeDate.getMonth() + 1
		const timeDay = timeDate.getDate()
		const timeHours = timeDate.getHours()
		const timeMinutes = timeDate.getMinutes()
		const timeSeconds = timeDate.getSeconds()
		if (cha < oneDay) {
			return `${repairZero(timeHours)}:${repairZero(timeMinutes)}:${repairZero(timeSeconds)}`
		} else if (cha >= oneDay && cha < oneDay * 2) {
			return '两天内'
		} else if (cha >= 2 * oneDay && cha < oneDay * 7) {
			return '七天内'
		} else {
			return `${repairZero(timeYear)}-${repairZero(timeMonth)}-${repairZero(timeDay)} ${repairZero(timeHours)}:${repairZero(timeMinutes)}:${repairZero(timeSeconds)}`
		}
	}

	function repairZero(num) {
		// console.log(num)
		if (num >= 0 && num < 10) {
			return '0' + num
		} else {
			return num
		}
	}
	export default {
		props: {
			// 自己的userId => 用于判断自己和别人
			userId: {
				type: String | Number,
				default: ''
			},
			// 历史消息
			historyList: {
				type: Array,
				default: () => []
			},
			// 消息列表
			messageList: {
				type: Array,
				default: () => []
			},
			// 標簽颜色配置項
			tagOptions: {
				type: Object,
				default: () => {}
			},
			// 列表key配置项
			defaultOptions: {
				type: Object,
				default: () => ({
					userId: 'userId',
					msgId: 'msgId',
					name: 'name',
					message: 'message',
					img: 'img',
					time: 'time',
					avator: 'avator',
					tagLabel: 'tagLabel'
				})
			},
			// 是否使用下拉
			useRefresh: {
				type: Boolean,
				default: true
			},
			// 图标大小
			iconSize: {
				type: String | Number,
				default: 30
			},
			// 自定义功能列表
			sheetList: {
				type: Array,
				default: () => {
					return [
						// {
						// 	img: '',
						// 	icon: 'photo',
						// 	name: '相册',
						// 	funLabel: 'photo',
						// },
					]
				}
			},
			// 是否保留默认自定义功能
			retainSheet: {
				type: Boolean,
				default: true
			},
			// 是否在点击加号后聊天列表移动到最下方
			useScrollBottom: {
				type: Boolean,
				default: true
			},
			// 显示时间的间隔	单位/毫秒
			intervalTime: {
				type: Number,
				default: 300000
			},
			// 默认头像
			errorIcon: {
				type: String,
				default: 'https://tva3.sinaimg.cn/large/9bd9b167gy1g4lhmt4zm5j21hc0xcnhs.jpg'
			}
		},
		watch: {
			sheetList: {
				handler: function() {
					setTimeout(() => {
						const query = uni.createSelectorQuery().in(this);
						let showBox = query.select('#show_box')
						let hideBox = query.select('#hide_box')
						hideBox.boundingClientRect((res) => {
							this.hideBoxHeight = res.height
						}).exec()
						showBox.boundingClientRect((res) => {
							this.showBoxHeight = res.height
						}).exec()
					})
				},
				immediate: true,
				deep: true
			},
			messageList: {
				handler: function(newVal, oldVal) {
					if (this.list.length > 0) {
						let addObj = newVal[newVal.length - 1]
						addObj.showTime = addObj.time - newVal[newVal.length - 2].time >= this.intervalTime
						addObj.timeLabel = disposeTime(newVal[newVal.length - 2].time)
						this.list.push(addObj)
					} else {
						// 是否显示时间
						newVal.map((item, index) => {
							item.showTime = index == 0 ? true : item.time - newVal[index - 1].time >= this
								.intervalTime
							item.timeLabel = disposeTime(item.time)
						})
						this.list = newVal
					}
				},
				immediate: true,
				deep: true
			},
			historyList: {
				handler: function(newVal, oldVal) {
					newVal.map((item, index) => {
						item.showTime = index == 0 ? true : item.time - newVal[index - 1].time >= this
							.intervalTime
						item.timeLabel = disposeTime(item.time)
					})
					let list = newVal.concat(this.list)
					console.log(list, 'listHistory')
					this.list = list
					// newVal.concat(this.list)
				},
				immediate: true,
				deep: true
			}
		},
		data() {
			return {
				sendVal: '',
				footerFlag: false,
				scrollToId: '',
				freshing: false,
				triggered: true,
				hideBoxHeight: 0,
				showBoxHeight: 0,
				defaultSheet: [{
					img: '',
					icon: 'camera',
					name: '拍摄',
					default: 'playCamera'
				}, {
					img: '',
					icon: 'photo',
					name: '相册',
					default: 'playPhoto'
				}],
				list: []
			}
		},
		computed: {
			bottomHeight() {
				return this.footerFlag ? (this.hideBoxHeight + this.showBoxHeight + 'px') : this.showBoxHeight + 'px'
			},
			sheet() {
				let sheet = this.sheetList
				const flag = sheet && sheet.length > 0
				return flag ? (this.retainSheet ? this.defaultSheet.concat(sheet) : sheet) : this.defaultSheet
			}
		},
		methods: {
			// 下拉刷新被触发
			onRefresh() {
				if (this.freshing) return;
				const stop = () => {
					this.triggered = false
					this.freshing = false
				}
				this.freshing = true;
				this.$emit('onRefresh', stop.bind(this))
			},
			// 下拉刷新被复位
			onReset() {
				this.triggered = 'restore'; // 需要重置
				// this.$emit('onReset')
			},
			scrollBottom() {
				// #ifdef APP-PLUS
				setTimeout(() => {
					this.scrollToId = 'y-chat-' + this.list[this.list.length - 1][this.defaultOptions.msgId]
					// console.log(this.scrollToId, 'id2')
				}, 100)
				// #endif
				// #ifndef APP-PLUS
				this.scrollToId = 'y-chat-' + this.list[this.list.length - 2][this.defaultOptions.msgId]
				// console.log(this.scrollToId, 'id1')
				setTimeout(() => {
					this.scrollToId = 'y-chat-' + this.list[this.list.length - 1][this.defaultOptions.msgId]
					// console.log(this.scrollToId, 'id2')
				})
				// #endif
			},
			showHideBox() {
				this.footerFlag = !this.footerFlag
				if (this.footerFlag && this.useScrollBottom) {
					setTimeout(() => {
						this.scrollBottom()
					}, 500)
				}
			},
			closeFooter() {
				this.footerFlag = false
			},
			lookImg(img) {
				const current = this.messageList.findIndex(item => item.img == img)
				uni.previewImage({
					current,
					urls: this.messageList.map(item => item.img).filter(item => item),
					// #ifndef MP-WEIXIN
					indicator: 'number',
					loop: true,
					// #endif
					longPressActions: {
						itemList: ['发送给朋友', '保存图片'],
						success(res) {
							console.log(res, 'res')
							console.log(this, 'this')
						}
					}
				})
			},
			moreFun(item) {
				item.default ? this[item.default]() : this.$emit(item.funLabel, item)
			},
			playCamera() {
				uni.chooseVideo({
					success(res) {
						console.log(res)
						this.$emit('playCamera', res)
					},
					fail(err) {
						console.log(err, 'err')
					}
				})
			},
			playPhoto() {
				uni.chooseImage({
					success: (res) => {
						console.log(res, 'imgRes')
						this.$emit('playPhoto', res)
					},
					fail: (err) => {
						console.log(err, 'imgErr')
					}
				})
			},
			send() {
				this.$emit('send', this.sendVal)
				this.sendVal = ''
				this.scrollBottom()
			}
		}
	}
</script>
<style lang="scss">
	@mixin msgBox ($diretion, $position, $symbol) {
		content: '';
		display: block;
		position: absolute;
		#{$diretion}: auto;
		#{$position}: 0;
		top: 10rpx;
		width: 0;
		height: 0;
		// transform: translate(100%);
		transform: translateX(#{$symbol}90%);
		border: 15rpx solid transparent;
		border-#{$diretion}-color: #fff
	}

	@keyframes fade_show {
		0% {
			opacity: 0;
			display: none;
		}

		10% {
			opacity: 0.1;
			display: block;
		}

		100% {
			opacity: 1;
			display: block;
		}
	}

	@keyframes width_animate_mini {
		0% {
			width: 116rpx;
		}

		50% {
			width: 98rpx;
		}

		100% {
			width: 80rpx;
		}
	}

	@keyframes width_animate_large {
		0% {
			width: 80rpx;
		}

		50% {
			width: 98rpx;
		}

		100% {
			width: 116rpx;
		}
	}

	.y-wrap {
		overflow: hidden;
		height: 100%;
		width: 100%;
		position: absolute;
		top: 0px;
		bottom: 0px;

		&_message_content {
			background-color: #f5f5f5;
			height: calc(100% - var(--bottom-height));
			transition: height 0.5s;

			&_box {
				padding: 10rpx 30rpx;
				height: auto;

				&>view:last-child {
					display: flex;
					justify-content: flex-start;
					align-items: flex-start;
				}

				&_msg {
					max-width: 40vw;
					display: flex;
					flex-direction: column;
					align-items: flex-start;
					padding: 0 20rpx;

					&__name {
						// padding: 0 10px 5px;
						padding-bottom: 10rpx;
						height: 56rpx;
						font-size: 24rpx;
						display: flex;
						align-items: center;

						&>text {
							padding: 0 10px;
						}
					}

					&__my {
						flex-direction: row-reverse;
					}
				}

				&_my {
					align-items: flex-end;
				}

				&__val {
					background-color: #fff;
					display: flex;
					justify-content: flex-start;
					align-items: center;
					flex-wrap: wrap;
					padding: 15rpx;
					border-radius: 10rpx;
					position: relative;
				}

				&__my {
					justify-content: flex-end;
					background-color: #3a9af6;
					color: #fff;
				}

				&__img {
					width: 40vw;
				}

				&__val::before {
					@include msgBox(right, left, '-')
				}

				&__my::before {
					@include msgBox(left, right, '+') border-left-color: #3a9af6;
				}
			}

			&_my {
				&>view:last-child {
					flex-direction: row-reverse;
				}
			}
		}

		&_footer {
			width: 100%;
			background-color: #fff;

			&_show_box {
				padding: 20rpx;
				box-sizing: border-box;
				height: 140rpx;
				display: flex;
				align-items: center;

				&__ipt {
					flex: 1;
				}

				.fade_show {
					animation: fade_show .3s linear;
				}

				.width_to_mini {
					animation: width_animate_mini .3s linear;
				}

				.width_to_large {
					animation: width_animate_large .3s linear;
				}

				view:last-child {
					display: flex;
				}
			}

			&_hide_box {
				width: 100%;
				display: flex;
				flex-wrap: wrap;

				&_item {
					width: 25%;
					display: flex;
					justify-content: center;
					padding-bottom: 20rpx;

					&__btn {
						display: flex;
						flex-direction: column;
						justify-content: space-between;
						align-items: center;
					}
				}
			}
		}
	}
</style>
