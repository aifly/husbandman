<template>
	<div  class="symbin-login-ui lt-full" :style="{background: 'url('+imgs.loginBg+') no-repeat center center',backgroundSize:'cover' }">
		 <div class="symbin-login-form" :style="{background:'url('+imgs.loginFormBg+') no-repeat center center',backgroundSize:'contain'}">
			 <img :src="imgs.loginFormBg" alt="" style="opacity:0">
			 <div class="symbin-login-C">
				 <div class="symbin-login-error"></div>
				 <div class="symbin-login-form-item">
					 <span><img :src="imgs.loginPerson" alt=""></span><input type="text" v-model="username"/>
				 </div>
				 <div class="symbin-login-form-item">
					 <span><img :src="imgs.loginLock" alt=""></span><input type="password" v-model="password"/>
				 </div>

				 <div class="symbin-login-btn">
					 登录
				 </div>

				 <div class="symbin-forget-pass">
					 <span>注册</span>  ｜ <span>忘记密码</span>
				 </div>
			 </div>
		 </div>
	</div>
</template>

<script>
	import './index.css';
	import $ from 'jquery';
	import symbinUtil from '../lib/util';

	import Vue from "vue";

	export default {
		props:['obserable'],
		name:'zmitiindex',
		data(){
			return{
				imgs:window.imgs,
				username:'',
				password:'',
				isLogined:false,
				isMove:false,
				showError:false,
				errorMsg:'',
				viewH:document.documentElement.clientHeight
			}
		},
		components:{
		},
		
		methods:{
			toastError(msg =  '用户名不能为空'){
				this.errorMsg = msg;
 				this.showError = true;
 				setTimeout(()=>{
 					this.errorMsg = '';
 					this.showError = false;
 				},2000)
			},
			login(){
				var _this = this;

				if(!this.username){
					this.toastError();
 					return;
				}
				if(!this.password){
					this.toastError('密码不能为空');
 					return;
				}
				symbinUtil.ajax({
					url:window.config.baseUrl+'/admin/adminlogin',
					data:{
						adminusername:_this.username,
						adminpwd:_this.password
					},
					fn(data){
						if(data.getret === 0){
							var param = data;
							delete param.getret;
							delete param.getmsg;
							symbinUtil.clearCookie('login');
							symbinUtil.setCookie('login',JSON.stringify(param),1);
							window.location.hash = '/console/';
							_this.$Message.success('登录成功~')
							_this.isLogined = true;
							window.location.reload();
						}else{
							_this.toastError(data.getmsg);
						}
					}
				})
			},
			 
			 

		},
		mounted(){

 			 
 			 

 			
		}
	}
</script>
 