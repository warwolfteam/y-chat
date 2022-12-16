<template>
	<view class="y-wrap" :style="'--bottom-height:' + bottomHeight">
		<scroll-view 
			class="y-wrap_message_content"
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
			@tap="closeFooter"
		>
			<view
				v-for="(item, index) in list"
				:key="index"
				:id="'y-chat-' + item[defaultOptions['msgId']]"
				class="y-wrap_message_content_box"
				:class="{ 'y-wrap_message_content_my' : item[defaultOptions['userId']] == userId }"
			>
				<image 
					class="y-wrap_message_content_box__avator" 
					:src="item[defaultOptions['avator']]" 
				></image> 
				<view class="y-wrap_message_content_box_msg">
					<view class="y-wrap_message_content_box_msg__name" :class="{ 'y-wrap_message_content_box_msg__my' : item[defaultOptions['userId']] == userId }">
						<u-tag 
							v-if="tagOptions[item[defaultOptions['tagLabel']]]"
							:bgColor="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].color : ''" 
							:borderColor="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].color : ''" 
							:text="tagOptions[item[defaultOptions['tagLabel']]] ? tagOptions[item[defaultOptions['tagLabel']]].text : ''" 
							size="mini"
						></u-tag>
						
						<!-- <text>{{ item[defaultOptions['name']] }}</text> -->
						<text>{{ item[defaultOptions['name']] }}</text>
					</view>
					<view
						class="y-wrap_message_content_box__val"
						:class="{ 'y-wrap_message_content_box__my' : item[defaultOptions['userId']] == userId }"
					>
						<view :style="{ textAlign: item[defaultOptions['userId']] == userId ? 'right' : 'left' }" v-if="item[defaultOptions['message']]">{{ item[defaultOptions['message']] }}</view>
						
						<image 
							@tap="lookImg(item[defaultOptions['img']])" 
							v-if="item[defaultOptions['img']]"
							:src="item[defaultOptions['img']]"
							class="y-wrap_message_content_box__img"
							mode="widthFix"
						></image>
					</view>
				</view>
				<!-- <view class="y_message_content_box_read">
					{{item[defaultOptions['read']]?'已读':'未读'}}
				</view> -->
			</view>
		</scroll-view>
		<view class="y-wrap_footer">
			<view class="y-wrap_footer_show_box" id="show_box">
				<u-icon custom-style="padding: 0 10rpx" :size="iconSize" name="mic"></u-icon>
				<view class="y-wrap_footer_show_box__ipt">
					<u-input v-model="sendVal"></u-input>
				</view>
				<view>
					<u-icon custom-style="padding: 0 10rpx" :size="iconSize" name="star"></u-icon>
					<!-- #ifndef MP-WEIXIN -->
						<u-icon
							customStyle="padding: 0 10rpx;" 
							:size="iconSize" 
							name="plus" 
							@click="showHideBox" 
							:class="!sendVal ? 'width_to_mini' : 'fade_show'" 
							v-if="!sendVal" 
						></u-icon>
						<button
							style="width:116rpx; 60rpx; padding: 0; text-align: center;" 
							type="primary" 
							size="mini" 
							:class="sendVal ? 'width_to_large' : 'fade_show'"
							v-else
						>发送</button>
					<!-- #endif -->
					<!-- #ifdef MP-WEIXIN -->
						<u-transition :show="!sendVal">
							<u-icon
								customStyle="padding: 0 10rpx;" 
								:size="iconSize" 
								name="plus" 
								@click="showHideBox" 
								v-if="!sendVal" 
							></u-icon>
						</u-transition>
						<u-transition :show="sendVal">
						  <button
							style="width:116rpx; 60rpx; padding: 0; text-align: center;" 
							type="primary" 
							size="mini" 
							v-if="sendVal" 
							@click="send"
						  >发送</button>
					   </u-transition>
					<!-- #endif -->
				</view>
			</view>
			<view class="y-wrap_footer_hide_box" id="hide_box">
				<view class="y-wrap_footer_hide_box_item">
					<view class="y-wrap_footer_hide_box_item__btn">
						<u-icon :size="iconSize" name="camera"></u-icon>
						<text>拍摄</text>
					</view>
				</view>
				<view class="y-wrap_footer_hide_box_item" v-for="(item, index) in sheetList" :key="index">
					<!-- <image src="" mode=""></image> -->
					<view class="y-wrap_footer_hide_box_item__btn" @click="item.conductFun ? item.conductFun : moreFun(item)">
						<img v-if="item.img" :src="item.img" style="width: 60rpx;" mode="aspectFill">
						<u-icon v-else :size="iconSize" :name="item.icon"></u-icon>
						<text>{{ item.name }}</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		props: {
			// 自己的userId => 用于判断自己和别人
			userId: {
				type: String | Number,
				default: ''
			},
			// 消息列表
			messageList: {
				type: Array,
				default:() => []
			},
			// 標簽颜色配置項
			tagOptions: {
				type: Object,
				default: () => {}
			},
			// 列表key配置项
			defaultOptions: {
				type: Object,
				default:() => (
					{
						userId: 'userId',
						msgId: 'msgId',
						name: 'name',
						message: 'message',
						img: 'img',
						time: 'time',
						avator: 'avator',
						tagColor: 'tagColor',
						tagLabel: 'tagLabel'
					}
				)
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
				type: Array | Object,
				default: () => {
					return [
						// {
						// 	img: 'https://tva3.sinaimg.cn/large/9bd9b167gy1g4lhmt4zm5j21hc0xcnhs.jpg',
						// 	icon: 'camera',
						// 	name: '拍摄',
						// 	funLabel: 'camera',
						// 	// conductFun: () => {}
						// },
					]
				}
			},
			// 是否在点击加号后聊天列表移动到最下方
			scrollBottomFlag: {
				type: Boolean,
				default: true
			}
		},
		watch: {
			sheetList: {
				handler:function(){
					// #ifndef MP-WEIXIN
						this.$nextTick(() => {
							const query = uni.createSelectorQuery().in(this);
							let showBox = query.select('#show_box')
							let hideBox = query.select('#hide_box')
							hideBox.boundingClientRect((res) => {
								this.hideBoxHeight = res.height
							}).exec()
							showBox.boundingClientRect((res) => {
								console.log(res,'res')
								this.showBoxHeight = res.height
								console.log(this.showBoxHeight,'asdasd')
							}).exec()
						})
					// #endif
					// #ifdef MP-WEIXIN
						wx.nextTick(() => {
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
					// #endif
				},
				immediate: true,
				deep: true
			},
		},
		data(){
			return {
				sendVal: '',
				footerFlag: false,
				scrollToId: '',
				freshing: false,
				triggered: true,
				hideBoxHeight: 0,
				showBoxHeight: 0
			}
		},
		computed: {
			bottomHeight(){
				return this.footerFlag ? (this.hideBoxHeight + this.showBoxHeight + 'px') : this.showBoxHeight + 'px'
			},
			list(){
				const list = this.messageList
				if(list && list.length > 0){
					list.map((item, index) => {
						if(index == 0) {
							item.showTime = true
						}else{
							item.showTime = item.createTime - list[index - 1].createTime >= 5 * 60
						}
					})
				}
				return list
			}
		},	
		methods: {
			// 下拉刷新被触发
			onRefresh(){
				if (this.freshing) return;
				const stop =  () => {
					this.triggered = false
					this.freshing = false
				}
				this.freshing = true;
				this.$emit('onRefresh', stop.bind(this))
			},
			// 下拉刷新被复位
			onReset(){
				this.triggered = 'restore'; // 需要重置
				// this.$emit('onReset')
			},
			scrollBottom(){
				this.scrollToId = 'y-chat-' + this.messageList[this.messageList.length - 2][this.defaultOptions.msgId]
				setTimeout(() => {
					this.scrollToId = 'y-chat-' + this.messageList[this.messageList.length - 1][this.defaultOptions.msgId]
				})
			},
			showHideBox(){
				this.footerFlag = !this.footerFlag
				if(this.footerFlag && this.scrollBottomFlag){
					setTimeout(() => {
						this.scrollBottom()
					},500)
				}
			},
			closeFooter(){
				this.footerFlag = false
			},
			lookImg(img){
				const current = this.messageList.findIndex(item => item.img == img)
				uni.previewImage({
					current,
					urls: this.messageList.map(item => item.img).filter(item => item),
					// #ifndef MP-WEIXIN
					indicator: 'number',
					loop: true,
					// #endif
					longPressActions: {
						itemList: ['发送给朋友','保存图片'],
						success(res) {
							console.log(res,'res')
							console.log(this,'this')
						}
					}
				})
			},
			moreFun(item){
				this.$emit(item.funLabel,item)
			},
			send(){
				this.$emit('send',this.sendVal)
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
		&_box{
			display: flex;
			justify-content: flex-start;
			align-items: flex-start;
			padding: 10rpx 30rpx;
			height: auto;
			&__avator{
				width: 80rpx;
				height: 80rpx;
				border-radius: 10rpx;
				background-color: red;
				padding-top: 10rpx;
			}
			&_msg {
				max-width: 40vw;
				display: flex; 
				flex-direction: column; 
				padding: 0 15rpx;
				&__name {
					// padding: 0 10px 5px;
					padding-bottom: 5px;
					font-size: 24rpx;
					display: flex;
					align-items: center;
					text {
						padding: 0 10px;
					}
				}
				
				&__my {
					flex-direction: row-reverse;
				}
			}
			&__val {
				background-color: #fff;
				display: flex;
				justify-content: flex-start;
				align-items: center;
				flex-wrap: wrap;
				padding: 15rpx;
				margin-left: 15rpx;
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
				@include msgBox(left, right, '+')
				border-left-color: #3a9af6;
			}
		}
		
		&_my {
			flex-direction: row-reverse;
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
			
			view:last-child{
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
