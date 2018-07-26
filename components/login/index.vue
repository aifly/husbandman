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
							<span class="symbin-getcode" @click="getCode">获取验证码</span>
						</div>
					</TabPane>
				</Tabs>
				<div class="symbin-login-btn" @click="login()">
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
					<span class="symbin-getcode" @click="getRegCode">
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
				<div class="symbin-reg-btn" @click='register'>
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
			register(){
				var s = this;
				symbinUtil.ajax({
					url:window.config.baseUrl+'/farmer/regist_farmer/',
					data:{
						usermobile:s.regMobile,
						verifycode:s.regCode,
						userpwd:s.regPassword
					},
					success(data){
						console.log(data);
					}
				})
			},
			getRegCode(){
				if(this.isSend){
					return;
				}
				if (!this.regMobile || !this.isPoneAvailable(this.regMobile)) {
					this.$Message.error('请输入正确的手机号');
					return;
				}
				var {regMobile} = this;
				this.isSend = true;
				var t = setInterval(()=>{
					this.seconds--;
					if(this.seconds<=0){
						clearInterval(t);
						this.isSend = false;
						this.seconds = 60;
					}
				},1000);
				 
				$.ajax({
					url:window.config.baseUrl+'/user/send_mobilecode/',
					type:'post',
					isNeedLogin:false,
					data:{
						setmobile:regMobile,
						usertype:2,//用户注册类型：1, 注册地主,2: 注册农夫
						smstype:1 //短信类型：1,注册；2,登陆；
					},
					success(data){
						console.log(data);
					}
				}) 
			},
			getCode(usertype=2,smstype=1) {

				if(this.isSend){
					return;
				}
				if (!this.mobile || !this.isPoneAvailable()) {
					this.$Message.error('请输入正确的手机号');
					return;
				}
				var {mobile} = this;
				this.isSend = true;
				var t = setInterval(()=>{
					this.seconds--;
					if(this.seconds<=0){
						clearInterval(t);
						this.isSend = false;
						this.seconds = 60;
					}
				},1000);
				 
				$.ajax({
					url:window.config.baseUrl+'/user/send_mobilecode/',
					type:'post',
					isNeedLogin:false,
					data:{
						setmobile:mobile,
						usertype,//用户注册类型：1, 注册地主,2: 注册农夫
						smstype //短信类型：1,注册；2,登陆；
					},
					success(data){
						console.log(data);
					}
				}) 
			},
			isPoneAvailable(type = this.mobile) {
				var myreg = /^[1][3,4,5,7,8][0-9]{9}$/;
				if (!myreg.test(type)) {
					return false;
				} else {
					return true;
				}
			},
			login(){
				var _this = this;

				/* if(!this.username){
					this.toastError();
 					return;
				}
				if(!this.password){
					this.toastError('密码不能为空');
 					return;
				} */
				symbinUtil.ajax({
					url:window.config.baseUrl+'/farmer/login/',
					data:{
						usermobile:_this.mobile,
						verifycode:_this.vilatecode,
						usertype:2
					},
					success(data){
						console.log(data);
						if(data.getret === 0){
							var param = data;
							return;
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

			window.s = this;
 			 

 			
		}
	}
</script>
 