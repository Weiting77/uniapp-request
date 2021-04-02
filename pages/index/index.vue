<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		
		<button @click="login()">登录</button>
		<button @click="showData()">显示数据</button>
	</view>
</template>

<script>
	
	import uni_request from'../../request/request.js'
	
	const request = uni_request({ // 有效配置项只有三个
		baseURL: 'https://www.laterh.cn', //baseURL
		timeout: 12345, // 超时时间，单位毫秒。默认 60 秒
		statusCode: [200, 401] // 服务器相应状态码为 200/401 时，网络请求不会 reject。也就是不会被 catch 到。如响应 401 时可以在响应拦截后 await 刷新 token + await 重新请求 + return response。即可实现无痛刷新。 
	})
	
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {
		},
		methods: {
			login(){
					request.get('/getToken', { account: '123' }, { 'Content-Type': 'application/x-www-form-urlencoded' }, false, true).then(res => {
							uni.setStorage({
											key:'token',
											data:res
										})
					}).catch(e => console.error(e));
			},
			showData(){
						let s = "";
						let token =uni.getStorageSync('token');
						console.log("token:"+token);
						request.get('/isExpiration', {}, { 'Content-Type': 'application/x-www-form-urlencoded','token':token}, false, true).then(res => {
							if(res == "G"){
								this.title = "token过期请重新登陆!";
							}else if(res == "S"){
								this.title = "token失效!";
							}else if(res == "F"){
								this.title = "非法的token!";
							}else {
								request.get('https://jsonplaceholder.typicode.com/posts', {  }, { 'Content-Type': 'application/x-www-form-urlencoded' }, false, true).then(res => {
										console.log("res:"+ res[0].title);
										this.title = res[0].title;
								}).catch(e => console.error(e));
							}
						}).catch(e => console.error(e));
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
