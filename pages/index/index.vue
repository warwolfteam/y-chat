<template>
	<view>
		<!-- <cola-u-chat :userId="userId" :messageData="list"></cola-u-chat> -->
		<y-u-chat 
			:userId="userId"
			:messageList="list" 
			:defaultOptions="defaultOptions"
			:tagOptions="tagOptions"
			@onRefresh="getNext"
			@camera="camera"
		></y-u-chat>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userId: 1,
				tagOptions: {
					1:{
						text: '管理员',
						color: '#ff2321'
					},
				},
				defaultOptions: {
					userId: 'userId',
					msgId: 'id',
					name: 'name',
					message: 'message',
					img: 'img',
					time: 'time',
					avator: 'avator',
					label: 'label',
					tagColor: 'tagColor',
					tagLabel: 'tagLabel'
				},
				list: [
					{
						userId: 1,
						id: 1,
						name: '白',
						message: 'ddd',
						avator: '',
						img: 'https://tva3.sinaimg.cn/large/9bd9b167gy1g4lhmt4zm5j21hc0xcnhs.jpg'
					},
					{
						userId: 2,
						id: 2,
						name: '黑',
						message: 'aaa',
						avator: '',
						img: require('@/static/ces.png')
					},
					{
						userId: 2,
						id: 3,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 4,
						name: '黑',
						message: 'aaa',
						avator:''
					},
					{
						userId: 2,
						id: 5,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 6,
						name: '黑',
						message: 'aaa',
						avator:''
					},
					{
						userId: 2,
						id: 7,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 8,
						name: '黑',
						message: 'aaa',
						avator:''
					},
					{
						userId: 2,
						id: 9,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 10,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 11,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 12,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 13,
						name: '黑',
						message: 'ccc',
						avator:''
					},
					{
						userId: 2,
						id: 14,
						name: '黑',
						message: 'ccc',
						avator:'',
						// tagColor: '#000',
					},
					{
						userId: 1,
						id: 15,
						name: '黑',
						message: 'cc11c',
						avator:'',
						tagLabel: 1
					}
				]
			}
		},
		onLoad() {
		},
		methods: {
			getNext(stop){
				// 请求接口 || ... 之后调用stop方法停止动画
				setTimeout(() => {
					stop()
					const list = [
						{
							userId: 1,
							id: 16,
							name: '黑',
							message: 'cc121c',
							avator:'',
						},
						{
							userId: 1,
							id: 17,
							name: '黑',
							message: 'cc121c',
							avator:'',
						},
						{
							userId: 1,
							id: 18,
							name: '黑',
							message: 'cc121c',
							avator:'',
						},
						{
							userId: 1,
							id: 19,
							name: '黑',
							message: 'cc121c',
							avator: '',
							tagLabel: 1
						}
					]
					list.map(item => this.list.unshift(item))
				}, 3000)
			},
			camera(e){
				console.log(e,'ee')
			}
		}
	}
</script>

<style>
</style>
