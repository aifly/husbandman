<template>
	<div  class="symbin-login-ui lt-full" :style="{background: 'url('+imgs.loginBg+') no-repeat center center',backgroundSize:'cover' }">
		 <div class="symbin-login-form" v-if='isLogin' :style="{background:'url('+imgs.loginFormBg+') no-repeat center center',backgroundSize:'contain'}">
			 <img :src="imgs.loginFormBg" alt="" style="opacity:0">
			 <div class="symbin-login-title">
				 <h2>智慧休闲农业方案提供商</h2>
			 </div>
			 <div class="symbin-login-C">
				<div class="symbin-login-error"></div>

				<Tabs value="loginType" v-model="loginType">
					<TabPane label="账号登录" name="0">
						<div class="symbin-login-form-item">
							<span><img :src="imgs.loginPerson" alt=""></span><input type="text" v-model="username"/>
						</div>
						<div class="symbin-login-form-item">
							<span><img :src="imgs.loginLock" alt=""></span><input type="password" v-model="password"/>
						</div>

						
					</TabPane>
					<TabPane label="手机登录" name="1">
						<div class="symbin-login-form-item">
							<span ><img style="width:15px;" :src="imgs.mobileIco" alt=""></span><input type="text" v-model="mobile"/>
						</div>
						<div class="symbin-login-form-item">
							<span><img :src="imgs.emailIco" alt=""></span><input type="text" v-model="vilatecode"/>
							<span class="symbin-getcode">获取验证码</span>
						</div>
					</TabPane>
				</Tabs>
				<div class="symbin-login-btn">
					登录
				</div>

				 <div class="symbin-forget-pass">
					 <span @click="isLogin = false">注册</span>  ｜ <span>忘记密码</span>
				 </div>
			 </div>
		 </div>

		 <div v-else class="symbin-login-form" :style="{background:'url('+imgs.loginFormBg+') no-repeat center center',backgroundSize:'contain'}">
			<img :src="imgs.loginFormBg" alt="" style="opacity:0">
			<div class="symbin-reg-title">
				<h2>用户注册</h2>
			</div>
			<div class="symbin-reg-C">
				<div class="symbin-reg-item">
					<input type="text" v-model="regMobile" placeholder="请输入账号（手机号）">
				</div>
				<div class="symbin-reg-item">
					<input type="text" v-model="regCode" placeholder="请输入验证码">
					<span class="symbin-getcode">
						获取验证码
					</span>
				</div>
				<div class="symbin-reg-item">
					<input type="password" v-model="regPassword" placeholder="请输入密码">
				</div>
				<div class="symbin-reg-item">
					<input type="password" v-model="regSurePassword" placeholder="请输入确认密码">
				</div>
				<div class="symbin-agreement">
					<input type="checkbox" v-model="agree" id="agree" /><label for="agree">我已阅读并接受<span>《云耕农夫注册协议》</span></label>
				</div>
				<div class="symbin-reg-btn">
					注册
				</div>

				<div class="symbin-go-login">
					已有账户，<span @click="isLogin = true">去登录>></span>
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
				loginType:'0',
				password:'',
				agree:false,
				isLogin:true,
				vilatecode:'',
				mobile:'',
				isLogined:false,
				isMove:false,
				regMobile:"",
				regCode:"",
				regPassword:"",
				regSurePassword:"",
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
 