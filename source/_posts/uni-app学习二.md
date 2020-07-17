---
title: uni-app学习二
date: 2020-07-16 19:29:14
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及组件传值。

<!-- more -->

#### 1、uni-app的开发方式

##### 1.1 使用HBuilder来快速创建项目

##### 1.2 使用脚手架来搭建和开发项目

个人观点，脚手架搭建是每个前端开发都必须要会的，但是之后开发为了快速构建使用HBuilderX快速构建就可以了

- 搭建步骤

  - 首先要具备node/vue-cli3开发环境

  - 创建项目 vue create -p dcloudio/uni-preset-vue my-project

  - 启动项目 cd my-project进入项目目录  npm run dev:mp-weixin

    注：

    >1、为什么没有直接用npm server？
    >
    >因为我们需要小程序编译所以使用这中方式，在项目搭建之后的 package.json中我们都可以看到
    >
    >2、如果vue-cli版本为2.0版本可输入一下命令
    >
    >`npm uninstall -g vue-cli`卸载2.0版本脚手架
    >
    >`npm install -g @vue/cli`安装2.0版本
    >
    >3、在微信小程序中进行编译要注意不能将整个文件夹放进去，这样小程序不会识别
    >
    >我们需要找到`my-project/dist/dev/mp-weixin`将这个文件放入开发者工具，之后微信小程序便会自动监听更改，进行编译

#### 2、pages、pages.json的相关内容

##### 2.1 pages.json

- pages.json里面的相关页面配置以及导航条的样式更改基本与微信小程序是相同的
  - 如果需要将哪个页面作为启动项便把它放为启动项
  - 只是之前在小程序中每个页面的title是在页面json中进行配置，在uni-app中可以直接在`path`后面的`style`对象里进行样式配置
  - 注意：页面配置的选项会覆盖点应用配置的内容，要是想显示应用配置的内容可以直接删除页面里面配置的样式

##### 2.2 pages：

- 在pages文件夹中，新创建页面会自动生成新的.vue文件
  - 但是这个现象在使用vscode编辑器时是不会不会自动创建的，需要手动进行创建
  - pages文件夹下新建的文件目录与文件名并不要求必须相同，但是为了之后开发方便建议大家写成相同的文件名，如果是不同的名字在pages.json页面进行引入时就需要注意一下

#### 3、css样式

##### 3.1 uni-app内置代有sass的相关配置

- 首先安装依赖：npm install sass-loader node-sass
- 然后再进行开发时需要在`style`后面加上`lang=scss`
- 而且uni-app对于sass的一些内置配置，eg.颜色等我们都可以直接使用

```
<template>
	<view class="content">
		<view class="scss">
			scss
		</view>
	</view>
</template>
<style lang="scss">
.content{
	.scss{
		background: red;
		color: $uni-color-primary;  //这个就是直接使用了uni-app的内置sass相关配置
	}
}
</style>
```

##### 3.2 全局引入css样式

- 要想全局引入css需要在`App.vue`中进行全局引入
- 但是要注意在`App.vue`中进行引入时不可以使用`@`符进行引入,因为如果这样的话uni-app是找不到的
- 如果static文件夹中的内容基本引入路径为`./static/common.css` 

#### 4、组件传值

如果我们使用驼峰命名法引入组件，uni-app推荐我们使用`-`进行使用组件

##### 4.1 父子传值

```
子组件：
<template>
	<view>
		<image :src="src" mode="widthFix"></image>
		<text>{{msg}}</text>
	</view>
</template>

<script>
	export default {
		props:['src','msg'],  //第四步：使用props接收父组件传值
		data() {
			return {};
		}
	}
</script>

父组件
<template>
	<view>
		<my-border :src="src1" :msg="msg1"></my-border>  //第三步: 使用子组件
		<my-border :src="src12"></my-border>
	</view>
</template>

<script>
	import myBorder from '@/components/my-border.vue';  //第一步：引入子组件
	export default {
		components:{  //第二步：注册子组件
			myBorder 
		},
		data() {
			return {
				//为要传递的参数
				src1:'https://www.189.cn/client/wap/wapclient/cloudmeeting/images/index_admin.png',  
				src12:'https://www.189.cn/client/wap/wapclient/cloudmeeting/images/index_video.png',
				msg1:'beautiful'
			}
		}
	}
</script>

```

##### 4.2 子父传值

```
子组件：
<template>
	<view> 
		<image @click="handleParents" :src="src" mode="widthFix"></image>  //第一步：使用事件进行触发
		<text>{{msg}}</text>
		
	</view>
</template>

<script>
	export default {
		props:['src','msg'],
		data() {
			return {};
		},
		methods:{
			handleParents:function(){
				this.$emit("getChildren",this.src)  //第二步：使用$emit进行发送要改变的数据 参数：定义的事件 要修改的参数
			}
		}
	}
</script>

父组件：
<template>
	<view>
		<my-border @getChildren="getData" :src="src1" :msg="msg1"></my-border>  //第三步：使用接收事件进行操作
		<my-border @getChildren="getData" :src="src12"></my-border>
	</view>
</template>

<script>
	import myBorder from '@/components/my-border.vue';
	export default {
		components:{
			myBorder
		},
		data() {
			return {
				src1:'https://www.189.cn/client/wap/wapclient/cloudmeeting/images/index_admin.png',
				src12:'https://www.189.cn/client/wap/wapclient/cloudmeeting/images/index_video.png',
				msg1:'beautiful'
			}
		},
		methods: {
			getData:function(e){  //第四步：操作的函数
				console.log(e)
			}
		}
	}
</script>
```

##### 4.3 全局共享数据

> 在uni-app中的父子传值与子父传值，基本与vue组件传值没有区别
>
> 但是全局共享数据有些变化，主要是增加了golbalData进行全局数据共享
>
> 具体分为：

- 使用common.js进行全局数据共享

  ```
  1、引用公共common.js
  export default {
      memberObj:{
          name:'初始姓名',
      },
      setMemberObj(data){
          this.memberObj = Object.assign({},this.memberObj,data) 
      }
  }
  2、在全局main.js中引用
  import Vue from 'vue'
  import App from './App'
  import member from './common/common.js'
  
  Vue.config.productionTip = false
  
  Vue.prototype.$member = member
  
  App.mpType = 'app'
  
  const app = new Vue({
    ...App
  })
  app.$mount()
  3、在普通页面获取全局变量，重新赋值
  changeCommon:function(){
      let that = this;
      let obj = {
          name:'爱尚',
          sex:'男'
      }
      that.$member.setMemberObj(obj);
      console.log(this.$member.memberObj);  //{name: "爱尚", sex: "男"}
  },
  
  ```

- 通过vue的状态管理工具vuex管理全局变量

  ```
  1、创建store文件夹，index.js
  //1、创建store文件，store.js
  import Vue from 'vue'
  import Vuex from 'vuex'
  
  Vue.use(Vuex)
  
  const store = new Vuex.Store({
      state: {
          memberData:'',
          initName:''
      },
  	//同步，处理state模块里的数据
      mutations: {
          copy(state,cont){
              //单一的改变某一个变量
              console.log(state)  //整个state数据池
              console.log(cont)  //传入的变量
              state.memberData = cont;  //在这里将传入的变量赋值给state.memberData
          },
          change(state,contObj){
              //通过传入的变量去改变对应的全局变量
  			console.log(contObj)  //contObj传入的变量
              let str = contObj.str;  //contObj.str，contObj.cont是传入的内容
              let cont = contObj.cont;
              state[str] = cont;  //指定state中的某一个变量去接收传入的对象
          },
      },
  	//异步，进行处理数据
      actions:{
          copeFun:function(context,mData){
  			console.log(context)  //存放了所有的内容
              context.commit('copy',mData)  //在这里对mutations中方法进行处理
          },
          changeFun:function(context,obj){
              context.commit('change',obj)  //在这里对mutations中方法进行处理
          }
      }
  })
  
  export default store
  2、在main.js中引入store.js
  import Vue from 'vue'
  import App from './App'
  import store from './store/index.js'
  
  Vue.config.productionTip = false
  
  Vue.prototype.$store = store
  
  App.mpType = 'app'
  
  const app = new Vue({
  	store,
    ...App
  })
  app.$mount()
  3、页面中获取需要使用的全局变量
  直接通过全局挂载的那种方式去获取 （定义在计算属性中是为了方便实时的监听变量重新赋值）
  computed:{
     memberData:function(){
        return this.$store.state.memberData;
     },
  }
  4、重新赋值vuex中的全局变量
  //单一方法改变指定的变量
  changeMember:function(){
      let mem = {
          name:'爱尚丽明',
          age:'28'
      }
      this.$store.dispatch('copeFun',mem)  //使用action进行调用
  },
  //通过传入要改的变量名进行改变变量
  changeMemberPub:function(){
      let memberData = {
          name:'爱尚',
          age:25
      }
      let $obj = {}
      $obj.cont = memberData ;   //cont = 新建的对象
      $obj.str = 'memberData'  //str = 要改变哪一个变量
      this.$store.dispatch('changeFun',$obj)
  }  
  ```

- 使用globalData操作全局数据

  个人理解：globalData内部的数据不能相互访问，如果想在App.vue中进行修改就只能通过生命周期函数进行修改

  ```
  1、在App.vue中使用globalData
  <script>
  	export default {
  		globalData:{
  			base:'www.360.com'
  		}
  	}
  </script>
  2、在普通页面获取globalData
  console.log(getApp().globalData.base);
  3、在页面修改globalData
  getApp().globalData.base = 'www.baidu.com'
  ```

  