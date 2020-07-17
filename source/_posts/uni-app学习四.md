---
layout: uni-app学习三
title: uni-app学习四
date: 2020-07-16 19:33:06
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及数据缓存、下拉刷新、下拉加载、跨端兼容、跳转页面、H5跨域。

<!-- more -->

#### 1、数据缓存

- uni-app中数据存储提供给我们两种方式，分为同步接口和异步接口

- 同步和异步的区别：异步不会阻塞当前任务，同步缓存直到同步方法处理完才能继续往下执行，这个大家应该都知道(我就给我自己说一下~)

- 缓存在不同端的实现方式是不同的，详见[数据缓存最后注意事项](https://uniapp.dcloud.io/api/storage/storage?id=clearstoragesync)

  ```
  异步缓存
  <button type="default" @click="set">异步数据存储</button>
  <button type="default" @click="get">异步数据获取</button>
  <button type="default" @click="remove">异步数据移除</button>
  <button type="default" @click="clear">清除所有数据</button>
  
  set:function(){
      uni.setStorage({
          key:'name',
          data:'saner',
          success() {
              console.log('存储成功')
          }
  	})
  },
  get:function(){
      uni.getStorage({
          key:'name',
          success(res) {
              console.log(res)
          }
      })
  },
  remove:function(){
      uni.removeStorage({
          key:'name',
          success() {
          	console.log('移除成功')
          }
      })
  },
  clear:function(){
  	uni.clearStorage()
  }
  
  同步缓存
  <button type="default" @click="set">同步数据存储</button>
  <button type="default" @click="get">同步数据获取</button>
  <button type="default" @click="remove">同步数据移除</button>
  <button type="default" @click="clear">清除所有数据</button>
  
  set:function(){
  	uni.setStorageSync('name','nannan')
  },
  get:function(){
      var name = uni.getStorageSync('name')
      console.log(name)
  },
  remove:function(){
  	uni.removeStorageSync('name')
  },
  clear:function(){
  	uni.clearStorageSync()
  }
  ```

#### 2、下拉刷新、上拉加载

##### 2.1 下拉刷新

- 简单理解就是，用户操作当前页面向下滑动

- 开启下拉刷新，必须要在pages.json中进行配置开启下拉刷新

  ```
  {
      "path" : "pages/goods/goods",
      "style" : {
          "navigationBarTitleText": "商品列表",
          "enablePullDownRefresh":true
          }
  }
  ```

- 触发下拉刷新操作可以用户手动进行触发，也可以通过`uni.startPullDownRefresh()`进行触发

  ```
  onPullDownRefresh(){
      console.log('下拉刷新了');
      this.pageindex = 1;
      this.goods = [];
      this.flag = false;
      setTimeout(()=>{
          this.getHotGoods(()=>{
          	uni.stopPullDownRefresh()
      });
      },1000)
  }
  ```

- 在操作完成后可以使用`uni.stopPullDownRefresh()`结束

##### 2.2 上拉加载 

- 通常用于加载下一页数据，也就是当前页面滑动到底部

- 页面上拉触底事件触发时距页面底部距离，可以使用style中的`onReachBottomDistance`属性进行调节，单位仅支持px

  ```
  onReachBottom() {
      if(this.goods.length<this.pageindex*10){  //当goods数据列表中的数据小于当前页面*每次返回10条时，则说明没有数据了
      	return this.flag = true;
      }
      this.pageindex++;
      this.getHotGoods();
  },
  ```

#### 3、跨端兼容

- 条件编译是用特殊的注释作为标记，在编译时根据这些特殊的注释，将注释里面的代码编译到不同平台，[详见](https://uniapp.dcloud.io/platform?id=跨端兼容)

- **写法：**以 #ifdef 或 #ifndef 加 **%PLATFORM%** 开头，以 #endif 结尾，开头和结尾缺一个就会报错

  ```
  <template>
  	<view>
  		<!-- #ifdef H5 -->
  		我在h5页面
  		<!-- #endif -->
  		<!-- #ifdef MP-WEIXIN -->
  		我在微信小程序页面
  		<!-- #endif -->
  	</view>
  </template>
  
  <script>
  	export default {
  		data() {
  			return {
  				
  			}
  		},
  		onLoad(options) {
  			// #ifdef H5
  			console.log('我在h5页面')
  			//#endif
  			// #ifdef MP-WEIXIN
  			console.log('我在微信小程序页面')
  			//#endif
  			console.log(options)
  		},
  		methods: {
  			
  		}
  	}
  </script>
  
  <style>
  /* #ifdef H5 */
  view{color: #007AFF;}
  /* #endif */
  /* #ifdef MP-WEIXIN */
  view{color: #4CD964;}
  /* #endif */
  </style>
  
  ```

#### 4、跳转页面

| 跳转方式               | [uni.redirectTo](https://uniapp.dcloud.io/api/router?id=redirectto) | [uni.switchTab](https://uniapp.dcloud.io/api/router?id=switchtab) | [uni.reLaunch](https://uniapp.dcloud.io/api/router?id=relaunch) | [uni.navigateBack](https://uniapp.dcloud.io/api/router?id=navigateback) | [uni.navigateTo](https://uniapp.dcloud.io/api/router?id=navigateto) |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 是否支持参数拼接       | 是                                                           | 否                                                           | 是                                                           |                                                              | 是                                                           |
| 是否会关闭上一次页面   | 是                                                           | 关闭所有的非tabBar页面                                       | 是                                                           | 是                                                           | 否                                                           |
| 是否支持tabBar页面跳转 | 否                                                           | 是                                                           | 是                                                           |                                                              | 否                                                           |
| 作用                   |                                                              |                                                              |                                                              | 返回上一页面或多级页面                                       |                                                              |

- 分为导航式跳转和编程式跳转

  ```
  <template>
  	<view>
  		<navigator url="../storage/storage?id=20&name=saner">跳转缓存页</navigator>
  		<navigator url="../jump/jump?id=20&name=saner" open-type="switchTab">跳转跨段兼容</navigator>
  		<navigator url="../storage/storage" open-type="redirect">跳转缓存页</navigator>
  		<navigator url="../jump/jump?id=20&name=saner" open-type="reLaunch">跳转缓存页</navigator>
  		<button type="default" @click="getDatail">跳转缓存页</button>
  		<button type="default" @click="getswitch">跳转缓存页</button>
  		<button type="default" @click="getdirect">跳转缓存页</button>
  		<button type="default" @click="getLaunch">跳转缓存页</button>
  	</view>
  </template>
  
  <script> 
  	export default {
  		methods: {
  			getDatail:function(){
  				uni.navigateTo({
  					url:'../storage/storage'
  				})
  			},
  			getswitch:function(){
  				uni.switchTab({
  					url:'../jump/jump'
  				})
  			},
  			getdirect:function(){
  				uni.redirectTo({
  					url:'../storage/storage'
  				})
  			},
  			getLaunch:function(){
  				uni.reLaunch({
  					url:'../storage/storage'
  				})
  			}
  		},
  		onUnload() {
  			console.log('页面被卸载了')
  		}
  	}
  </script>
  
  navigateBack:
  go:function(){
      uni.navigateBack({
      	delta:2
      })
  }
  ```

##### 5、遇到的问题

- 跨域问题的处理的方法：

  - 在manifest.json中源码视图配置proxy代理

  ```
  "h5": {
  		"devServer" : {
  			"proxy" : {
  				"/api" : {
  					"target": "https://lm.189.cn",  //请求得域名
  					"changeOrigin": true,  //是否跨域
  					"secure": false,  //设置支持https协议代理
  					"pathRewrite":{  //路径重写
  						"^/api":""
  					}
  				}
  			}
  		}
  	}
  ```

  - 在设置完跨域代理之后我们得请求url就要有所改变，在所有的小程序端是不会出现跨域问题的，只有H5页面会

  - 所以我们要记得使用条件注释进行区分一下页面

  - 最后就是要切记需要重新编译一下，因为你更改了它的配置文件

    