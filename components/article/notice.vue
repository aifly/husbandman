<template>
	<div class="symbin-article-ui">
		<div class="symbin-article-title">
			<Row>
		        <Col span="6"><h5>公告管理</h5></Col>
		        <Col span="18">
		        	<div class="text-right"><Button type="primary" @click="open">新增</Button></div>
		        </Col>
		    </Row>
		</div>
		<div class="top20"></div>
		<Table :columns="columns1" :data="listData"></Table>
		<div class="top20"></div>
		<Page :total="total" @on-change="setpages" :page-size="3"></Page>
		<div class="top20"></div>
		<Modal
	        v-model="modal1"
	        title="公告">
	        <div class="symbin-article-addinfo">
	        	<Form ref="formItem" :model="formItem" :rules="ruleValidate" :label-width="80">
					<FormItem label="标题" prop="title">
			            <Input v-model="formItem.title" placeholder="标题"></Input>
			        </FormItem>			        		        
			        <FormItem label="内容" prop="content">
			            <Input v-model="formItem.content" type="textarea" :rows="4" placeholder="内容"></Input>
			        </FormItem>
			        <FormItem label="状态" prop="status">
			            <RadioGroup v-model="formItem.status">					        
					        <Radio label="1"><span>开</span></Radio>
					        <Radio label="0"><span>关</span></Radio>
					    </RadioGroup>
			        </FormItem>
	        	</Form>
	        </div>
	        <div slot="footer">
	            <Button type="text" @click="cancel('formItem')">取消</Button><Button type="primary" @click="asyncOK('formItem')" >确定</Button>
	        </div>
	    </Modal>
	</div>	
</template>
<script>
	import './index.css';
	import sysbinVerification from '../lib/verification';
	import symbinUtil from '../lib/util';
	import Vue from 'vue';
	export default {
		props:['obserable'],
		name:'zmitiindex',

		data(){
			return {
				modal1: false,
				currentIndex:'',
				total:10,//总条数
				pagesize:3,//每页数量
				formItem:{
					title:'',
					content:'',
					status:'1'
				},
				columns1: [
                    {
                        title: '标题',
                        key: 'title'
                    },
                    {
                        title: '时间',
                        key: 'createtime',
                        width:150,
                    },
                    {
                        title: '状态',
                        key: 'status',
                        width:150,
                        render:(h,params)=>{                        	
                        	return h('Icon',{
                        		props:{
                        			type:params.row.status==1?'checkmark-circled':'minus-circled'
                        		},
                        		style:{
                        			fontSize:'20px',
                        			color:params.row.status==1?'#19be6b':'#ed3f14'
                        		}
                        	})
                        }
                    },
                    {
                        title: '操作',
                        key: 'action',
                        align: 'left',
                        width:150,
                        render: (h, params) => {
                        	return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'default',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                        	this.show(params.row.noticeid);
                                        }
                                    }
                                }, '修改'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {                                            
                                            this.remove(params.row.noticeid);
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                listData: [],
                ruleValidate: {
                    title: [
                        { required: true, message: '标题不能为空', trigger: 'blur' }
                    ],
                    content: [
                        { required: true, message: '内容不能为空', trigger: 'blur' }
                    ]
                }
			}
		},
		components:{
		},

		beforeCreate(){
			this.validateData = sysbinVerification.validate(this);
			//symbinUtil.clearCookie('login');

		},
		methods:{
			open(){
				var s = this;
				s.modal1=true;
				s.currentIndex='';
				s.formItem={
					noticeid:'',
					title: '',
					content: '',
					status:'1'
				}
				console.log(s.currentIndex,'s.currentIndex');
			},
            cancel (name) {
                this.modal1=false;            
                this.$refs[name].resetFields();
            },
            show(ids){
            	var s = this;
            	s.modal1=true;
            	s.currentIndex=ids;
				s.listData.forEach(function(value, index) {
					if(value.noticeid==ids){
						s.formItem={
							noticeid:ids,
							title: value.title,
							content: value.content,
							status:String(value.status)
						}										
					}								    
				});
            },
            add(){//增加
				var s = this;
				var formparams={
					title: s.formItem.title,
                    content:s.formItem.content
				}
				symbinUtil.ajax({
					url:window.config.baseUrl+"/admin/addnotice",
					validate:s.validateData,
					data:formparams,
					fn(data){
						if(data.getret === 0){
							s.$Message.success(data.getmsg);
							s.getListData();//刷新列表
						}
						else{
							s.$Message.error({
								content:data.getmsg,
								duration: 10
							});
						}
					}
				})				

			},
			edit(ids){//编辑
				var s = this;
				var formparams={
					noticeid:ids,
					title: s.formItem.title,
					content: s.formItem.content,
					status:s.formItem.status
				}
				
				symbinUtil.ajax({
					url:window.config.baseUrl+"/admin/editnotice",
					type:'post',
					validate:s.validateData,
					data:formparams,
					fn(data){						
						if(data.getret===0){
							s.$Message.success(data.getmsg);
							s.getListData();//刷新列表
						}
						else{
							s.$Message.error({
								content:data.getmsg,
								duration: 5
							});
						}						
					}
				})
			},
			asyncOK (name) {
				var s = this;	
                this.$refs[name].validate((valid) => {                    
                    if (valid) {                   
		                if(s.currentIndex!=''){
		                	this.edit(s.currentIndex);//保存编辑
		                }else{
		                	this.add();//保存新增
		                }
                        this.modal1=false;
                        
                    } else {
                        this.$Message.error('提交失败!');                        
                        this.modal1 = true;                                              
                    }
                })
            },
            getListData(index){//获取数据
				var s = this;
				symbinUtil.ajax({
					url:window.config.baseUrl+"/admin/getnoticelist",				
					type:'post',
					validate:s.validateData,
					data:{
						admin:s.validateData.adminusername,
						admintoken:s.validateData.admintoken,
						page:index,//页码
						pagenum:s.pagesize//每页数量
					},
					fn(data){						
						if(data.getret===0){
							console.log(data,'data');
							s.listData=data.list;
							s.total=4;//总条数
						}
						else{
							 s.$Message.error({
							  	content:data.getmsg,
							  	duration: 10
							  });
							 if(data.getret === 1300){
							 	window.location.hash = '/login/'
							 }
						}
						
					}
				})
			},
			remove(ids){//删除
				var s = this;
				symbinUtil.ajax({
					url:window.config.baseUrl+"/admin/delnotice",
					type:'post',
					validate:s.validateData,
					data:{
						noticeids:ids,
						deltype:1,
					},
					fn(data){						
						if(data.getret===0){
							s.$Message.success(data.getmsg);
							s.getListData();
						}
						else{
							s.$Message.error({
								content:data.getmsg,
								duration: 5
							});
						}						
					}
				})
			},
			setpages(index){
				var s = this;
				s.getListData(index);
			}
		},
		mounted(){//页面加载完成后显示
			this.getListData(1);			
		},
	}
</script>
 