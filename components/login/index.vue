<template>
	<div  class="symbin-login-ui lt-full" :style="{background: 'url('+imgs.loginBg+') no-repeat center center',backgroundSize:'cover' }">
		 <div class="symbin-login-form" v-if='isLogin' :style="{background:'url('+imgs.loginFormBg+') no-repeat center center',backgroundSize:'contain'}">
			 <img :src="imgs.loginFormBg" alt="" style="opacity:0">
			 <div class="symbin-login-title">
				 <h2>智慧休闲农业方案提供商</h2>
			 </div>
			 <div class="symbin-login-C">
				<div class="symbin-login-error">{{errorMsg}}</div>

				<Tabs value="loginType" v-model="loginType">
					<TabPane label="账号登录" name="0">
						<div class="symbin-login-form-item">
							<span><img :src="imgs.loginPerson" alt=""></span><input type="text" v-model="username"/>
						</div>
						<div class="symbin-login-form-item">
							<span><img :src="imgs.loginLock" alt=""></span><input type="password" v-model="password" @keydown.13='login'/>
						</div>

						
					</TabPane>
					<TabPane label="手机登录" name="1">
						<div class="symbin-login-form-item">
							<span ><img style="width:15px;" :src="imgs.mobileIco" alt=""></span><input type="text"  v-model="mobile"/>
						</div>
						<div class="symbin-login-form-item">
							<span><img :src="imgs.emailIco" alt=""></span><input type="text" v-model="vilatecode" @keydown.13='login' />
							<span class="symbin-getcode" @click="getCode">{{loginGetCodeText}}</span>
						</div>
					</TabPane>
				</Tabs>
				<div class="symbin-login-btn" @click="login()">
					登录 <Icon type="load-c" class="demo-spin-icon-load" v-if='showLoading'></Icon>
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
						{{regGetCodeText}}
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
				showLoading:false,
				mobile:'',
				isLogined:false,
				loginGetCodeText:'获取验证码',
				regGetCodeText :'获取验证码',
				isMove:false,
				regMobile:"",
				seconds:60,
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
				this.$Message.error(msg);
				/* this.errorMsg = msg;
 				this.showError = true;
 				setTimeout(()=>{
 					this.errorMsg = '';
 					this.showError = false;
 				},2000) */
			},
			register(){
				if(!this.regMobile){
					this.$Message.error('注册的手机号不能为空');
					return;
				}
				if(!this.regCode){
					this.$Message.error('注册的验证码不能为空');
					return;
				}
				if(!this.regPassword){
					this.$Message.error('注册的密码不能为空');
					return;
				}
				if(this.regPassword !== this.regSurePassword){
					this.$Message.error('注册的两次密码不一致');
					return;
				}
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
						if(data.getret === 0){
							s.$Message.success('注册成功')
						}else{
							s.$Message.error('注册失败')
						}
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
				this.regGetCodeText = this.seconds+'秒后重新发送'
				var t = setInterval(()=>{
					this.seconds--;
					this.regGetCodeText = this.seconds+'秒后重新发送'
					if(this.seconds<=0){
						clearInterval(t);
						this.isSend = false;
						this.seconds = 60;
						this.regGetCodeText = '获取验证码'
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
			getCode() {

				if(this.isSend){
					return;
				}
				
				if (!this.mobile || !this.isPoneAvailable()) {
					this.$Message.error('请输入正确的手机号');
					return;
				}

				var {mobile} = this;
				this.isSend = true;
				this.loginGetCodeText = this.seconds+'秒后重新发送'
				var t = setInterval(()=>{
					this.seconds--;
					this.loginGetCodeText = this.seconds+'秒后重新发送'
					if(this.seconds<=0){
						clearInterval(t);
						this.isSend = false;
						this.seconds = 60;
						this.loginGetCodeText = '获取验证码'
					}
				},1000);

				 
				$.ajax({
					url:window.config.baseUrl+'/user/send_mobilecode/',
					type:'post',
					isNeedLogin:false,
					data:{
						setmobile:mobile,
						usertype:2,//用户注册类型：1, 地主,2: 农夫
						smstype:1 //短信类型：0,注册；1,登陆；
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
				var params = {
					
				};
				switch (this.loginType) {
					case '0':
						if(!this.username){
							//this.$Message.error('用户名不能为空')
							_this.toastError('用户名不能为空');
							return;
						}
						if(!this.password){
							//this.$Message.error('密码不能为空')
							_this.toastError('密码不能为空');
							return;
						}
						params.userpwd = _this.password,
						params.logintype = 2;//1，短信登陆，2：密码登陆
						params.usermobile = _this.username;
						break;
				
					case '1':
						if(!this.mobile){
							_this.toastError('用户名不能为空');
							//this.$Message.error('用户名不能为空')
							
							return;
						}
						if(!this.vilatecode){
							_this.toastError('验证码不能为空');
						//	this.$Message.error('验证码不能为空')
							return;
						}
						params.verifycode = _this.vilatecode
						params.logintype = 1;//1，短信登陆，2：密码登陆
						params.usermobile = _this.mobile;
						//params._this = _this;
						params.validate = _this.validate;
						break;
				}
				var  p1= params;
				this.showLoading = true;

				symbinUtil.ajax({
					url:window.config.baseUrl+'/farmer/login/',
					isLogin:true,
					data:params,
					error(){
						_this.showLoading = false;
					},
					success(data){
						_this.showLoading = false;
						if(data.getret === 0){
							var param = data;
							delete param.getret;
							delete param.getmsg;
						
							param.usermobile = p1.usermobile
						
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
<style>
	.demo-spin-icon-load{
        animation: ani-demo-spin 1s linear infinite;
    }
    @keyframes ani-demo-spin {
        from { transform: rotate(0deg);}
        50%  { transform: rotate(180deg);}
        to   { transform: rotate(360deg);}
    }

 </style>