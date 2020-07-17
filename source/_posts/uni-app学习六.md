---
title: uni-app学习六
date: 2020-07-16 19:34:11
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及colorUI、软键盘上移、获取用户位置、获取用户信息。

<!-- more -->

#### 1、摸鱼唠嗑

##### 1.1 加载中的loading框

之前做项目的时候太着急就没做请求时加载的动画效果，今天看官网的时候突然发现有自带的api组件，用的还不错，

但是官网也有大写的注意：

`showToast` 和 `showLoading` 是底层同一个（按的小程序的设计），所以 `showToast` 和 `showLoading` 会相互覆盖，而 `hideLoading` 也会关闭 `showToast`,

解决办法：就是尽量使用其中一个就可以了，避免同时使用

##### 1.2 仿vue-router的组件

[移步大佬的插件地址](https://user-gold-cdn.xitu.io/2019/8/8/16c704ff28803df5)

##### 1.3 colorUI组件

界面真的是十分好看，作为一个女生来说爱了，具体组件可以到官网进行下载，[地址](https://www.color-ui.com)

colorUI还提供了活跃的群文件：https://www.yuque.com/colorui

#### 2、踩坑日记

##### 2.1 uni-app微信端上传体验版

- 只要是上传都会校验端口号，看到很多说可以开启手机端调试，就是在使用小程序的时候可以看到页面的右上方有三个圆点，点击一下就会弹出一个底部的浮窗，选择开发调试就可以，但是我自己没有测试过，只是提供参考
- 记得添加白名单，刷新一下项目配置
- 不过这个特别容易出现微信缓存，测试的时候以为自己错了，研究半天换个手机就好了，我太难了

##### 2.2 软键盘上移页面变形

这个解决办法挺多的但是要看哪种比较适用了，以下举例：

1、具体思路：就是获取当前手机屏幕高度，然后当手机屏幕高度发生变化时与手机屏幕高度进行比较，如果小于则将底部按钮隐藏，大于再恢复

```
uni.getSystemInfo({  //这里调用小程序获取系统信息
    success: (res)=> {
    	this.windowHeight = res.windowHeight;
    }
});   
uni.onWindowResize((res) => {  //小程序api
    if(res.size.windowHeight < this.windowHeight){
    	this.isShowFlag = false;
    }else{
    	this.isShowFlag = true;
    }
});
```

弊端：底部按钮在消失之前会屏幕闪动一下

2、具体思路：在输入框获取焦点时，将底部定位使用position:static,再失去焦点时，将底部定位使用position:absoluted/fixed

弊端：就是使用static的时候如果页面的内容如果高度不够的话可能就会看见底部的内容

3、两种思路的实现方法相差不多，这里贴出第一种的代码，但是各有弊端，还有其他的思路，还是要根据自己的情况而定，大家酌情使用。

##### 2.3 改变引入的富文本的样式

只有在全局里改变才会做用到样式，局部改变是不起作用的。

##### 2.4 使用uni.showToast下方函数执行太快造成toast没有显示

要将即将执行的函数放入定时器中进行执行，并且要记得改变this指向或者直接使用箭头函数

```
uni.showToast({
    title: title,  
    icon: "none"
}); 
setTimeout(function(){
	that.getchartCode();
},2000)
```

#### 3、api使用

##### 3.1 uni-app微信小程序端获取用户位置

> 微信小程序提供给我们的获取位置的api有两种，[获取经纬度](https://uniapp.dcloud.io/api/location/location?id=getlocation)，[可以选择地区然后获取具体位置](https://uniapp.dcloud.io/api/location/location?id=chooselocation)
>
> 但是在项目中我需要使用详细位置而不是经纬度，也不是选择之后获取的位置，所以需要引入第三方地图的sdk
>
> 我这里使用高德，主要就是需要注册一个key来供我们使用，其余并不复杂，[参考示例](https://ask.dcloud.net.cn/article/35070)

![img](https://img-blog.csdnimg.cn/20200512173303704.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxNjc2NzI1,size_16,color_FFFFFF,t_70)



![img](https://img-blog.csdnimg.cn/20200512173544855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxNjc2NzI1,size_16,color_FFFFFF,t_70)

> 根据参考示例以及图示基本可以完成key值得获取了
>
> 接下来就上代码

```
<text style="color: #9B9B9B;">{{Areaaddress}}</text>

import amap from '../../common/amap-wx.js'

//1. uniapp弹窗弹出获取授权（地理，个人微信信息等授权信息）弹窗
getAuthorizeInfo(a="scope.userLocation"){  
    var _this=this;
    uni.authorize({
        scope: a,
        success() { //1.1 允许授权
        	_this.getLocationInfo();
        },
        fail(){    //1.2 拒绝授权
            uni.showToast({
                title: "你拒绝了授权，无法获得周边信息",  
                icon: "none"
            }); 
        }
    })
},
//2. 获取地理位置
getLocationInfo(){  
    var _this=this;
    uni.getLocation({
        type: 'gcj02',
        success (res) {
            _this.latitude = res.latitude.toString();  //纬度
            _this.longitude = res.longitude.toString();
            _this.loadCity(_this.latitude ,_this.longitude);
        }
    });
},
//获取精确位置
loadCity: function (latitude, longitude) {
    var that = this;
    var myAmapFun = new amap.AMapWX({ key: that.key });
    myAmapFun.getRegeo({
    location: '' + longitude + ',' + latitude + '',//location的格式为'经度,纬度'
    success: function (e) {
        let city,province;
        city= e[0].regeocodeData.addressComponent.city;//城市
        province= e[0].regeocodeData.addressComponent.province;//省份
        that.Areaaddress = province;
    },
    fail: function (info) {
        uni.showToast({
            title: info,  
            icon: "none"
    	}); 
    	}
    });
},
```

##### 3.2 uni-app微信小程序端获取用户信息

获取用户信息必须手动授权，不可以在直接授权

```
<template>
	<view class="content">
		<button open-type="getUserInfo" @getuserinfo="getuserinfo">登录</button>
		<!-- 用户信息模块 -->
			<view class="userBox">
				<!-- 昵称 -->
				<text class="nickName">{{nickNames}}</text>
				<!-- 头像 -->
				<image class="userIcon" :src="avatarUrl"></image>
			</view>
	</view>
</template>

<script>
	export default {
			data() {
				return {
					nickNames: '匿名用户',
					avatarUrl: 'https://www.189.cn/client/wap/wapclient/xingcard/images/index_avatar.png'
				}
			},
			onLoad() {
				
			},
			methods:{
				getuserinfo:function(){
					let that = this;
					uni.login({
						provider: 'weixin',
						success: function(loginRes) {
							// 获取用户信息
							uni.getUserInfo({
								provider: 'weixin',
								success: function(infoRes) {
									that._data.nickNames = infoRes.userInfo.nickName;
									that._data.avatarUrl = infoRes.userInfo.avatarUrl;
								}
							});
						}
					});
				}
			}
		}
	
</script>

<style>
	/* 用户盒子 */
		.userBox {
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 20px;
			background: linear-gradient(to top right, #ffd422  0%, #ffe944 25%, #fff970 100%);
		}
	 
		/* 用户昵称 */
		.nickName {
			color: #55240A;
		}
	 
		/* 用户头像 */
		.userIcon {
			align-self: flex-end;
			border-radius: 50%;
			overflow: hidden;
			width: 100px;
			height: 100px;
			box-shadow: 0 1px 2px rgba(0, 0, 0, 0.5)
		}
</style>

```

##### 3.3 省市区联动组件

这个在ui-app的组件库里面也有丰富的示例供我们挑选就不多做介绍了，在项目中我用的是[大家可以自行选择](https://ext.dcloud.net.cn/plugin?id=1472)

##### 3.4 h5端获取定位信息

> 搜索了半天看到一个特别麻烦的，真的是无从下手，要是想研究的可以学习一下，[详见](https://blog.csdn.net/qq_31676725/article/details/106080943?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-2)

最后使用了一个免费的接口获取地址，免费获取ip的网址特别多，相信大家百度就有，就不多说了。