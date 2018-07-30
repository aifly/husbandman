<template>
    <div class="layout">
        <Layout v-if='$route.name !== "login"'>
            <Header>
                <Row type='flex'>
                    <Col style="width: 200px;text-align: center" > 
                        <div class="layout-logo">
                            <img :src="imgs.logo"  />
                        </div>
                    </Col>
                    <Col>
                        <Menu mode="horizontal" theme="dark" active-name="1">
                            <div class="layout-nav">
                                <MenuItem :key='i' :name='i' v-for='(menu,i) in topMenu'>
                                    <router-link  :to="menu.link">{{menu.name}}</router-link>
                                </MenuItem>
                            </div>
                        </Menu>
                    </Col>
                </Row>
            </Header>
            <Layout>
                <Sider hide-trigger class='symbin-main-menu' :style='{height:(viewH - 64)+"px"}' >
                    <Menu active-name="1-2" theme="dark" width="auto" :open-names="['1']">
                        <Submenu :key='menu.name' :name="i*1+1" v-for='(menu,i) in menus'>
                            <template slot="title">
                                <Icon type="ionic"></Icon>{{menu.name}}
                            </template>
                            <MenuItem  :key='sm.name' :name="i+'-'+(k+1)" v-for='(sm,k) in menu.subMenu'>
                                <Icon type="ionic"></Icon>
                                <router-link :to='sm.link'>{{sm.name}}</router-link>
                            </MenuItem>
                        </Submenu>
                    </Menu>
                </Sider>
                <Layout>
                   <router-view></router-view>
                </Layout>
            </Layout>
        </Layout>
        <div v-else>
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
	import './index.css';
    import Vue from 'vue';
    import symbinUtil from '../lib/util';
    import sysbinVerification from '../lib/verification';

	export default {
		props:[],
		name:'zmitiindex',
		data(){
			return{
				imgs:window.imgs,
                viewH:document.documentElement.clientHeight,
                openNames:['1'],
                defaultLeftMenu:[],
                topMenu:[
                ],
                defaultMenu:[
                    {
                        name:'管理员设置',
                        subMenu:[
                            {
                                name:'系统管理员管理',
                                link:'/adminuser/'
                            }
                        ]
                    },{
                        name:'栏目设置',
                        subMenu:[
                            {
                                name:'后台栏目管理',
                                link:'/column/list'
                            }
                        ]
                    },{
                        name:'权限设置',
                        subMenu:[
                            {
                                name:'角色管理',
                                link:'/role/'
                            },
                            {
                                name:'权限管理',
                                link:'/purview/'
                            }
                        ]
                    }
                ],
                menus:[
                    
                ]
			}
		},
		components:{
		},
        beforeCreate(){
            //this.validateData = sysbinVerification.validate(this);

        },
        watch:{
           /*  $route(to){
                switch(to.name){
                    case 'rolepanel':
                        this.menus = this.defaultMenu;
                      
                    break;
                    case 'console':
                        this.menus = this.defaultLeftMenu;
                    break;
                }
               
            } */
        },
		mounted(){
           ///this.menus = this.defaultMenu.concat([]);
            var obserable = Vue.obserable;
            obserable.on('fillMenu',(data)=>{
                
                this.menus = data|| [];

            })
            switch(this.$route.name){
                case 'rolepanel':
                    this.menus = this.defaultMenu;
                break;
                case 'console':
                    this.menus = this.defaultLeftMenu;
                break;

            }
            if(this.$route.name === 'login'){
                return;
            }
            var obserable = Vue.obserable;

            
            this.loadMenu({
                status:1,
            },(data)=>{

                data.filter((dt,i)=>{
                    return dt.showWhere === 1
                }).map((dt,i)=>{
                    this.topMenu.push({
                        name:dt.name,
                        link:dt.link
                    })
                })

                this.menus = [
                    {
                        name:'商品管理',
                        subMenu:[
                            {
                                name:'我的商品',
                                link:'/mygoods/'
                            },{
                                name:'添加商品',
                                link:'/addgoods/'
                            },
                            
                        ]
                    },{
                        name:'订单管理',
                        subMenu:[
                            {
                                name:'我的订单',
                                link:'/myorder'
                            }
                        ]
                    },{
                        name:'我的客户',
                        subMenu:[
                            {
                                name:'我的客户',
                                link:'/mylandlorduser/'
                            }
                        ]
                    }
                ]


               

                
            }); 



        },
       
		methods:{
			
           
            loadMenu(option,fn){
                var s = this;
                var data = [
                    {  
                        name:'控制台',
                        link:'/console/',
                        showWhere:1,
                    },
                    {
                        name:'控制面板',
                        link:'/role/',
                        showWhere:1,
                    },
                    {
                        name:'',

                    }
                ]
                fn && fn(data);
                 
            }
		}
	}
</script>
 

 <style >
     .ivu-layout-header{
        padding: 0 !important; 
     }
     .ivu-layout{
        position: relative;
     }
 </style>