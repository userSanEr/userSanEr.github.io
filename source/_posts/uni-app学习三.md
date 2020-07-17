---
title: uni-app学习三
date: 2020-07-16 19:33:21
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及插槽、组件。

<!-- more -->

#### 1、插槽

在uni-app中插槽的使用与vue的用法基本一样。

```
子组件：
<template>
	<view>
		<text>我是传递的内容</text>
		<view class="content">
			<slot></slot>
		</view>
	</view>
</template>

父组件：
<template>
	<view>
		<my-slot>
			<view class="">我是父组件中的</view>
			<input type="text" value="" />
		</my-slot>
	</view>
</template>

<script>
	import mySlot from '../../components/my-slot.vue'
	export default {
		components:{
			mySlot,
		},
	}
</script>

```

#### 2、生命周期

- 在全局应用也就是App.vue中使用应用生命周期，等同于微信小程序所使用全局生命周期
- 在页面中使用页面生命周期，与微信小程序页面生命周期一样
- 对于引入的组件不可以使用页面生命周期，此时可以使用vue的生命周期
  - 相对来说比微信小程序,uni-app更适合会vue的人上手
  - 微信小程序组件也有自己单独的生命周期，[详见](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/lifetimes.html)

#### 3、uni-ui组件

- uni-ui是由Dclound自己推出关于uni-app的组件库，虽然uni-app支持小程序的所有基础组件，但是也有一定程度的局限性，[了解更多](https://ext.dcloud.net.cn/plugin?id=55)

- 相对应得npm包以及github都有详细得使用说明，可以根据自己需要引入组件进行使用

- 但是uni-app是使用sass进行编写得所以如果是使用用HBuilderX进行开发得话需要在工具栏安装sass编译的组件

- 如果是vscode可根据[5.29的笔记](https://juejin.im/post/5ed110aaf265da76e56735f0#heading-6)进行安装sass编译模块

- 在 HBuilderX 中新建 uni-app 项目，进入项目目录，执行：

  ```
  npm init -y
  ```

- 安装 uni-ui

  ```
  npm install @dcloudio/uni-ui
  ```

- 在 `template` 中使用组件：

  ```
  <template>
  	<view class="homework-ctn">
  		<uni-card :title='title' :isFull="isFull" :note="note" :thumbnail="thumbnail" :extra="extra"></uni-card>
  		<uni-pagination
  			show-icon=false
  			total=100 
  			pageSize=10
  			current=2
  			prev-text="上一页"
  			next-text="下一页"
  		></uni-pagination>
  		<uni-badge text="1"></uni-badge>
  		<uni-badge text="2" type="success" @click="bindClick"></uni-badge>
  		<uni-badge text="3" type="primary" :inverted="true"></uni-badge>
  		<uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" style-type="button" active-color="#4cd964"></uni-segmented-control>
  		        <view class="content">
  		            <view v-show="current === 0">
  		                选项卡1的内容
  		            </view>
  		            <view v-show="current === 1">
  		                选项卡2的内容
  		            </view>
  		            <view v-show="current === 2">
  		                选项卡3的内容
  		            </view>
  		        </view>
  	</view>
  </template>
  
  <script>
  	import {uniCard, uniPagination,uniBadge,uniSegmentedControl} from '@dcloudio/uni-ui'
  	export default {
  		components: {
  			uniCard,
  			uniPagination,
  			uniBadge,
  			uniSegmentedControl
  		},
  		data() {
  			return {
  				title: '快陪练',
  				extra: '教育科技公司',
  				note: '拓展钢琴陪练业务',
  				thumbnail: require('../../static/logo.png'),
  				isFull: true,
  				items: ['选项卡1','选项卡2','选项卡3'],
  				current: 0
  			}
  		},
  		onLoad() {
  
  		},
  		methods: {
  			onClickItem(e) {
  				if (this.current !== e.currentIndex) {
  					this.current = e.currentIndex;
  				}
  			}
  		}
  	}
  </script>
  
  ```

- 也可以单独安装组件，不用下载所有的组件

  - 在[uni-ui的扩展组件清单中](https://uniapp.dcloud.io/component/README?id=uniui)，点击每个组件在详情页面可以导入组件到项目下，导入后直接使用即可，无需import和注册。

#### 4、请求数据

- uni-app所使用的请求数据的方法与微信小程序的请求数据的方法类似，但是由于`wx.request`不支持`promise`

- uni-app对其进行了封装支持promise的使用，在开发使用时只需要将`wx.request`换成`uni.request`进行请求就可以，[详见](https://uniapp.dcloud.io/api/request/request)

- 一般在页面生命周期`onLoad`中进行调用

- 一般我们进入一个新的函数之后需要使用变量接收一下当前的this指向，或者使用箭头函数代替

  ```
  getSwipers:function(){
      uni.request({
          url:"http://localhost:8082/api/getlunbo",
          success:(res)=>{  
              if(res.data.status !== 0){
                  return uni.showToast({  //在这里使用uni-app自带组件进行弹窗提示
                  	title:"获取数据失败"
              	})
              }
              this.swipers = res.data.message  
              console.log(this.swipers)
          }
      })
  }
  ```


#### 5、遇到的小问题

在基本了解uni-app的API之后我开始上手实战，在这里对自己遇到的问题进行总结

- 查看颜色可以使用`shift`+鼠标左键切换颜色格式
- 因为调用接口使用大佬们的数据库，所以在这里使用php_study进行配合使用
  - 首先在首页面开启Mysql，如果在开启过程中，一直启动不了，可以尝试更改请求端口
  - 再进入`node.js`配置的请求js中使用`node app.js`进行启动服务
  - 这样就可以在浏览器进行访问了
- 使用computed对data池中的数组进行过滤操作时，一直提示map不是一个function
  - 在自己十分懵逼的研究半天之后，解决办法就是在刚开始注册数据的时候，如果为数组就写成空数组的形式，不要赋值为null/空字符串
