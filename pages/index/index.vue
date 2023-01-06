<template>
	<view>
		<y-chat
			:userId="userId"
			:messageList="list"
			:historyList="historyList"
			:defaultOptions="defaultOptions"
			:tagOptions="tagOptions"
			:sheetList="sheetList"
			@onRefresh="getNext"
			@playPhoto="playPhoto"
			@playCamera="playCamera"
			@send="send"
		></y-chat>
	</view>
</template>

<script>
	// 用来获取随机id的, 正式项目不需要
	function getRandomNum(){
		return Math.floor(Math.random() * 100).toString() + Math.floor(Math.random() * 100).toString()
	}
	export default {
		data() {
			return {
				userId: 1,
				tagOptions: {
					1:{
						text: '管理员',
						bgColor: '#ff4100',
						color: '#fff'
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
					tagLabel: 'tagLabel'
				},
				list: [
					{
						userId: 1,
						id: 1,
						name: '白',
						message: 'ddd',
						time: new Date().getTime(),
						avator: '',
						img: 'https://tva3.sinaimg.cn/large/9bd9b167gy1g4lhmt4zm5j21hc0xcnhs.jpg'
					},
					{
						userId: 2,
						id: 2,
						name: '黑',
						message: 'aaa',
						time: new Date().getTime(),
						avator: '',
						img: require('@/static/ces.png'),
						tagLabel: 1
					},
					{
						userId: 2,
						id: 3,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 4,
						name: '黑',
						message: 'aaa',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 5,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 6,
						name: '黑',
						message: 'aaa',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 7,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 8,
						name: '黑',
						message: 'aaa',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 9,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 10,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 11,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 12,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 13,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:''
					},
					{
						userId: 2,
						id: 14,
						name: '黑',
						message: 'ccc',
						time: new Date().getTime(),
						avator:'',
						// tagColor: '#000',
					},
					{
						userId: 1,
						id: 15,
						name: '白',
						message: 'cc11c',
						time: new Date().getTime(),
						avator:'',
						tagLabel: 1
					}
				],
				historyList:[],
				sheetList: [
				  {
					img: '',
					icon: 'red-packet-fill',
					name: '红包'
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
					this.getList()
					stop()
				}, 100)
			},
			getList(){
				// 假设list是获取到的历史消息
				const list = [
					{
						userId: 1,
						id: getRandomNum(), //不重复id
						name: '黑',
						message: 'cc121c',
						time: new Date().getTime(),
						avator:'',
					},
					{
						userId: 1,
						id: getRandomNum(),
						name: '黑',
						message: 'cc121c',
						time: new Date().getTime(),
						avator:'',
					},
					{
						userId: 1,
						id: getRandomNum(),
						name: '黑',
						message: 'cc121c',
						avator:'',
					},
					{
						userId: 1,
						id: getRandomNum(),
						name: '黑',
						message: 'cc121c',
						time: new Date().getTime(),
						avator: '',
						tagLabel: 1
					}
				]
				this.historyList = list
			},
			playPhoto(file){
				console.log(file,'file')
			},
			playCamera(file){
				console.log(file,'file')
			},
			send(val){
				this.list.push(
					{
						userId: 1,
						id: ++this.list[this.list.length - 1].id + 10, // id必须是唯一值
						name: '白',
						message: val,
						avator: 'https://tva3.sinaimg.cn/large/9bd9b167gy1g4lhmt4zm5j21hc0xcnhs.jpg',
						tagLabel: 1
					}
				)
			}
		}
	}
</script>

<style>
</style>
