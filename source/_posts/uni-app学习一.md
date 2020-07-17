---
title: uni-app学习一
date: 2020-07-16 19:19:16
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助。

<!-- more -->

#### 1、总结说明

- 主要使用HBuilderX配合微信开发者工具来进行学习

- 在发布微信小程序时需注意：

  - 个人账号只可以发布5个微信小程序，所以在学习过程中需要谨慎发布。

  - 如果发布之后想要添加体验人员与开发人员

    可以进入[小程序官网](https://mp.weixin.qq.com)然后使用自己账号登陆之后，在成员管理里面添加相关人员，各为15个；

    其余非个人的企业账号等会有所增加。

#### 2、HBuilderX快速创建项目快捷键

- 新建项目: `ctrl+n`之后数字键盘使用1即可创建项目
- 选择uni-app创建项目: `alt+u`

- 创建项目：`alt+u`
- 想要删除某一行的标签
  - 如果只是删除文字部分，可使用鼠标在标签结尾处点击，全部选中，`delete`删除
  - 如果是要删除这一行，包括前后的空格，则使用`ctrl+d`进行删除

#### 3、uni-app的文件夹目录认识

- uni-app的文件目录类似于微信小程序的目录，每个目录所存放的内容以及进行的功能也基本与微信小程序类似；
- [官网目录介绍]([https://uniapp.dcloud.io/frame?id=%e7%9b%ae%e5%bd%95%e7%bb%93%e6%9e%84](https://uniapp.dcloud.io/frame?id=目录结构))
- 个人认识
  - App.vue 存放全局生命周期以及全局样式，[详见](https://uniapp.dcloud.io/frame?id=应用生命周期)
  - main.js 主入口文件
  - manifest.json  配置应用名称、appid、logo、版本等打包信息，[详见](https://uniapp.dcloud.io/collocation/manifest)
  - pages.json 路由、选项卡等一系列配置，pages的第一项为启动页

#### 4、uni-app的组件

- uni-app与微信小程序的基础组件一样，包括调取接口虽然换成`uni.request`但是基本还是使用了微信小程序的方式
- script中一系列的方法包括`methods、data、生命周期等`都与`vue.js`的使用方法一样，但是还是有一些不支持的：
  - 不可以使用`html`的标签进行文档结构编写
  - 不能使用`vue.js`中的自定义过滤器
  - 事件修饰符、按键修饰符等也有很多不支持，仅`.stop`支持度较高
  - 表单组件也大都使用微信小程序自带的表单组件

#### 5、小的基本认识

- uni-app的线上项目：CSDN、咪咕商场、张亮麻辣烫、中国银联、中华英才网等

- 单位调整：uni-app支持微信小程序的rpx和h5页面的vw、vh作为单位

  ```
  <template>
  	<view class="content">
  		<view class="rpx">
  			rpx
  		</view>
  		<view class="vw">
  			vw
  		</view>
  	</view>
  </template>
  
  <script>
  	export default {
  		
  	}
  </script>
  
  <style lang="scss">
  /**小程序中的单位 750rpx=屏幕的宽度*/
  .rpx{width: 750rpx;height: 100px;background-color: aqua;}
  /**h5中的单位 100vw=屏幕的宽度  100vh=屏幕的高度*/
  .vw{width: 50vw;height: 100px;background: tan;}
  </style>
  
  ```

- 不同端开发需要不同展示：可以使用`<!-- #ifdef 8个不同平台 --><!-- #endif -->`进行判断处理

- 生命周期包括

  - 应用生命周期：基本与小程序生命周期一样,[详见](https://uniapp.dcloud.io/frame?id=应用生命周期)
  - 页面生命周期：基本与小程序生命周期一样，[详见](https://uniapp.dcloud.io/frame?id=页面生命周期)
  - vue生命周期：基本与vue使用方法一样，[详见](https://cn.vuejs.org/v2/api/#选项-生命周期钩子)

- data的用法

  - 一般使用函数形式，否则再次返回数据仍然存在，造成影响，其余使用方法与vue基本一样
  - 如果想在标签中通过属性的方式使用，必须使用：进行绑定，否则在编译时只会将data数据当作普通的字符串来处理

  ```
  <view class="content" :data-color="msg">
    	{{msg}}
   </view>
  data() {
      return {
      	msg : 'data中的信息'
      }
  },
  ```

- `vue.js`中的`v-for、v-if、computed`使用基本相同，要注意`v-for、v-if`两者一般情况下不推荐同时使用，要处理复杂的逻辑可以使用`computed`进行代替

  ```
  <template>
  	<view class="content">
  		<view class="" v-for="(item,index) in filterList" :key="index">{{item.text}}</view>
  </template>
  
  <script>
  	export default {
  		data(){
              return {
                  list:[
                      {
                          id:0,
                          text:'🍌'
                      },
                      {
                          id:1,
                          text:'🍒'
                      }
                  ]
              }
  		},
          computed:{
              filterList:function(){
                  return this.list.filter(v =>v.id<=0)
              }
          },
  	}
  </script>
  ```

- rich-text的使用，分为string以及array两种类型

  - [具体参考](https://uniapp.dcloud.io/component/rich-text?id=rich-text)
  - :nodes这个属性里面写的变量是自己在data中定义的
  - 对于我自己的理解：
    - 在nodes中不能直接插入dom结构
    - 怎么使用数组方式使用span等标签官网表示的并不是很明确，所以还是使用string更为方便

- [condition](https://uniapp.dcloud.io/collocation/pages?id=condition)

  - 在浏览器中查看页面我们可以直接使用路径`enter`键进行查看页面，但是在小程序中我们就只能这样使用了

  - 启动模式配置，仅开发期间生效，用于模拟直达页面的场景，如：小程序转发后，用户点击所打开的页面
  - 对于list说明中name属性，是自己定义的可以随意更改，只是在使用小程序编译之后要记得选择自己所要编译的是哪一个

#### 6、使用模拟器查看

- 使用微信小程序，如果在非第一次之后的运行时不可以自动调起微信小程序，可以参考如下解决方案

  - 在HBuilderX中进行配置专有的appid，这个在你登录之后也会自动生成

  - 在微信开发者工具中打开设置-安全设置-开放

- 使用真机查看 
  - 使用usb进行连接电脑 然后在手机系统调成开发者模式-打开usb调试即可进行真机测试
  - 如果显示没有检测到设备或模拟器的情况，需要在手机端与PC端分别下载360手机助手进行调试

#### 7、打包发行

- 在微信小程序中先将我们的appid改为自己的appid可在HBuilderX或微信小程序中进行更改

  - HBuilderX中将我们的微信小程序配置调成自己的appid重启微信开发者工具

  - 直接将微信小程序中详情配置的appid改为自己的
  - 然后点击上方的上传-等到审核通过-便可以发行

- 打包成app进行发行，以云打包为例

  - 发行-原生app云打包
  - 选择andriod/ios 使用cloud公用证书打包
  - 在打包过程中可以查看云打包状态，当打包完成之后，复制下载链接
  - 可将apk先下载到电脑上然后通过手机安装即可使用