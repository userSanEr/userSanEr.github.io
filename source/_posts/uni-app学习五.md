---
title: uni-app学习五
date: 2020-07-16 19:33:59
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及封装方法、打开弹窗禁止滚动、项目源码。

<!-- more -->

#### 1、封装接口以及规范

> 首先就是所有的页面公用的方法必须要在main.js中引入
>
> 然后挂载到Vue原型链上
>
> 然后就可以在所有的页面通过你挂载的属性名进行引用
>
> 很简单的步骤，相信大家上手都会很快

##### 1.1 请求数据接口封装

主要是对请求的过程进行一个封装，但是觉得其实还可以多加改善

params：options接收一个对象，对请求地址、请求方法以及数据进行传参

```
封装方法：
export const doAjax = (options) =>{
	return new Promise((resolve,reject)=>{
		uni.showLoading({
		    title: '加载中'
		});
		uni.request({
			url:options.url,
			method:options.method || 'GET',
			data:options.data || {},
			success:(res)=>{
				uni.hideLoading();
				resolve(res.data);
			},
			fail:(err) =>{
				uni.showToast({
					title:"系统繁忙，请稍后再试"  
				});
				reject(err);
			}
		})
	})
};
使用方式：
1、引入
import {doAjax,isPhoneNumber,handleWrap} from './common/util.js'
2、挂载
Vue.prototype.$doAjax = doAjax
3、使用
async getchartCode(){
    let param = {
        "headerInfo": { "functionCode": "getPicRandomCode"},
        "requestContent":{"sessionid":this.pageData.sessionid || ""}
    }
    const res = await this.$doAjax({
        //#ifdef H5
        url: 'http://wapapp01.zsc.189.cn/wapclient/queryAward.do',  //这里分两种方式主要是因为h5跨域问题
        //#endif
        //#ifndef H5 
        url: 'http://wapapp01.zsc.189.cn/wapclient/queryAward.do',
        //#endif
        method: 'POST',
        data: param
    })
}
```

##### 1.2  判断手机号是否符合

param：obj代表要进行正则判断的手机号码

```
封装方法
export const isPhoneNumber = (obj) =>{
	let phoneID = /^((13|14|15|16|17|18|19)){1}\d{9}$/;
	if(!phoneID.test(obj)){
		return false;
	}else{
		return true;
	}
};

使用方式：
if(!this.$isPhoneNumber(this.pageDom.phoneNum)){
    this.errTipFun(this.errTip.e001);  //手机号输入错误
    return false;
}
```

##### 1.3  对base64位长编码返回的换行进行处理

param：chart代表要进行转换的64位图片

```
封装方法：
export const handleWrap = (chart) =>{
	let base64 = chart.replace(/[\r\n]/g, "");
	return base64;
}

使用方式：
let res = this.$handleWrap(data.randomData)  //data.randomData代表base64位内容
```

#### 2、在开发过程中遇到的坑

> 干活首先脑子要灵活，我因为一点小问题也有困扰我一个小时的，太难了

##### 2.1 base64位图片不能显示

> 具体原因就是因为base64位返回的长编码代有换行，不能自己转换
>
> 但是uni-app底层又是通过背景图片进行显示图片的，base64位图片不经过处理通过img可以显示，但是使用背景就不好显示了

##### 2.2 图片与图片同时使用会有一点间距

> 因为啥我也不知道，我自己上手使用margin-top调了调

#### 3、总结的知识点

##### 3.1 在弹窗滑动阻止页面滚动

> 在要滚动的区域添加上` @touchmove.stop.prevent="moveHandle"`就可以阻止滚动
>
> 用的十分顺手啊！！

##### 3.2 手动实现倒计时60s

> 可能有什么bug 但是目前没发现 ，发现也别讲出来，把正确的发给我就好

```
方法：
reduceCount:function(){ 
    if(!this.pageDom.randomNum){  //倒计时结束
    	clearInterval(this.time);  //清除计时器
        this.pageDom.randomText = '重新获取'  //将文本改为重新获取
        this.getchartCode();  //获取校验码 
        this.flagArr.randombtnflag = true;  //获取验证码显示 倒计时隐藏
        this.pageDom.randomNum = 60;  //将倒计时重新开始
        this.pageDom.randomCode = '';  //将短信验证码文本置空
    }else{
    	--this.pageDom.randomNum;  //每隔1s减一次
    }
},
调用：
this.time = setInterval(this.reduceCount,1000);  //开启倒计时
```

##### 3.3 源码地址

[移步github](https://github.com/userSanEr/flowquery1)

虽然项目中并没有用到必须的tabbar还有跳转等基础的API，但是也学到了很多，项目虽小，但也可以积累经验。
