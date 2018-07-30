<template>
	<div class="symbin-goods-ui lt-full">
		<div class="symbin-goods-list">
			<h2>添加商品</h2>
			<ul>
				<li class="symbin-goodsclass-item" v-for='(goodsclass,i) in goodsList' :key="i">
					<div>
						{{goodsclass.goodsclassname}}
					</div>
					<span>
						
					</span>
					<ol >
						<li @click='getCurrentGoods(goods)' class="symbin-goods-item" v-for="(goods,k) in goodsclass.list" :key="k">
							<div :class="{'active':currentGoodsId === goods.goodsid}">
								{{goods.goodsname}}
							</div>
						</li>
					</ol>
				</li>
			</ul>
		</div>
		<div class="symbin-goods-detail">
			<section>
				<div>
					<header>
						添加商品
					</header>
					<div class='symbin-goods-content'>
						<div>
							{{formItem.goodsdesc}}
						</div>
						<div>
							 
						</div>
					</div>
				</div>
			</section>
			<section>
				<Button type="primary" @click='addGoods'>添加商品</Button>
			</section>
		</div>
		<div class="symbin-mygoods-list">
			<h2>我的商品</h2>
			<ul>
				<li v-for="(goods,i) in myGoodsList" :key="i">
					<div>
						<img :src="goods.imagepath" alt="">
					</div>
					<div>
						<div>{{goods.goodsname}}</div>
						<div>单价 <span>{{'￥'+goods.price}}</span></div>
						<div>数量 : {{goods.goodsnumber}}</div>
					</div>
					<div>
						<Button type="error" icon='ios-trash' size='small'>删除</Button>
					</div>
				</li>
			</ul>
		</div>

		<Modal v-model="modal1" title="添加商品">
	
			<div class="">
	
				<Form ref="formItem" :model="formItem" :label-width="100">
					<FormItem label="数量：" prop="goodsclassname">
						 <InputNumber style="width:100%" :max="9999" :min="1" v-model="formItem.goodsnumber"></InputNumber>
					</FormItem>
					<FormItem label="单价：" prop="sort">
						<Input v-model="formItem.goodsprice" placeholder="单价"></Input>
					</FormItem>
					<FormItem label="特色描述：" prop="sort">
						<Input type="textarea" v-model="formItem.goodsfeature" placeholder="特色描述"></Input>
					</FormItem>
				</Form>
	
			</div>
	
			<div slot="footer">
	
				<Button type="text" @click="cancel('formItem')">取消</Button>
	
				<Button type="primary" @click="asyncOK">确定</Button>
	
			</div>
	
		</Modal>
	</div>
</template>
<script>
	import './index.css';
	
	import sysbinVerification from '../lib/verification';
	import $ from 'jquery';
	
	
	import symbinUtil from '../lib/util';
	
	export default {
	
		props: ['obserable'],
	
		name: 'zmitiindex',
	
	
	
		data() {
	
			return {
				
				modal1: true,
				currentIndex:'',
				goodsList:[],
				myGoodsList:[],
				currentGoodsId:-1,
				formItem: {
					sort:1,
					goodsname: '', 
					goodsClass:[],
					goodsdesc:'',
					goodsnumber:1,
					goodscreatetime:'',
					createtime:'',
					imagepath:""

				}, 
	
			}
	
		},
	
		components: {},
	
	
	
		beforeCreate() {
	
			this.validateData = sysbinVerification.validate(this);
	
			//symbinUtil.clearCookie('login');
	
		},
	
		methods: {

			getCurrentGoods(goods){
				this.currentGoodsId = goods.goodsid;
				this.formItem = goods;
				console.log(this.formItem)
			},

			addGoods(){
				this.modal1 = true;
			},
			 
			getBaseGoodsList() { //获取数据
	
				var s = this;
				symbinUtil.ajax({
	
					url: window.config.baseUrl + "/admin/getgoodslist",
					url:'/components/goods/data.json',
	
					type: 'post',
					type:'get',
					data: {
						admin: s.validateData.adminusername,
						admintoken: s.validateData.admintoken
					},
					fn(data) {
						console.log(data);
						s.goodsList = data.list;
					}
				})
	
			},
			asyncOK(){
				var s = this;
				console.log({
						goodsid:s.formItem.goodsid,
						price:s.formItem.goodsprice,
						goodsnumber:s.formItem.goodsnumber,
						goodsfeature:s.formItem.goodsfeature
					})
				symbinUtil.ajax({
					url:window.config.baseUrl+'/farmer/shelfgoods/',
					data:{
						goodsid:s.formItem.goodsid,
						price:s.formItem.goodsprice,
						goodsnumber:s.formItem.goodsnumber,
						goodsfeature:s.formItem.goodsfeature
					},
					success(data){
						console.log(data);
						s.$Message[data.getret === 0 ?'success':"error"](data.getmsg);
					}
				})
			},
			cancel(){
				this.modal1 = false;
			},
			getMyGoodsList(){
				var s = this;
				symbinUtil.ajax({
					url:window.config.baseUrl+'/farmer/getmygoodslist/',
					data:{
						status:1
					},
					success(data){
						if(data.getret === 0){
							console.log(data.list);
							s.myGoodsList = data.list;
						}
					}
				})
			}
	
	 
	
		},
	
		mounted() { //页面加载完成后显示
			this.getBaseGoodsList();
			this.getMyGoodsList();
		},
	
	}
</script>
 