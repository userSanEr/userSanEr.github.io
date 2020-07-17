---
title: uni-app学习七
date: 2020-07-16 19:34:27
tags: [学习，uni-app]
categories: uniapp
---

关于跨端框架uni-app的总结学习，为了自己日后查看方便同时也希望可以提供给大家一些帮助，此篇主要涉及图片上传、外联跳转、js进行npm发布。

<!-- more -->

#### 1、注意事项

- 在uni-app中请求路径只能是绝对路径，不可以使用相对路径

#### 2、常用方法

##### 2.1 图片上传

uniapp有提供专门用于图片上传的就不多做介绍了，[详见](https://uniapp.dcloud.io/api/media/image?id=chooseimage)

```
uni.chooseImage({
    count:1,//设置一次能选择的图片的数量 
    sizeType:['original'],//指定是原图还是压缩,默认二者都有 ['original','compressed']
    sourceType:['album','camera'],//可以指定来源是相册还是相机,默认二者都有
    success:function(res){   //微信返回了一个资源对象
        that.imgArr = res.tempFilePaths;
        that.getImage(res.tempFilePaths[0]);
    }
});
```

##### 2.2 base64转码处理公共方法

在将选择上传的图片传给后台时，一般需要对图片进行转码处理

```
//方法：
export const getImageUrl = (src) =>{
	return new Promise((resolve,reject)=>{
		let base64 = ''
		uni.getFileSystemManager().readFile({
			filePath: src, //选择图片返回的相对路径
			encoding: 'base64', //编码格式
			success: res => { //成功的回调
				base64 = 'data:image/jpeg;base64,' + res.data; //不加上这串字符，在页面无法显示的哦
				resolve(base64)
			},
		})
	})
}

//调用
async getImage(data){
    let result = await this.$getImageUrl(data);
    this.picUp(result)
},
```

##### 2.3 外链跳转

uni-app提供的跳转方法，并不能进行跳转外部页面，这里对微信小程序端的外链跳转方法，进行示例：

我是用`webview`进行外链引入，然后进行跳转，应该还有更好的方法，可能自己没发现，这里只说明自己用到的，[具体使用方法详见](https://uniapp.dcloud.io/component/web-view)

```
//在内部页面中引入外部链接 然后直接使用uni跳转方法，跳转到这个链接就可以了
<template>
	<view>
		<web-view :src="url"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url:'https://www.189.cn/client/wap/common/page/error_404.html?errorCode=20001'
			}
		},
	}
</script>

//重点强调
如果想要在封装方法的js中进行跳转链接的使用不可以带有.vue后缀名，否则不能跳转
```

#### 3、实现js进行npm发布

> 在我们做完一系列的封装之后一般都想提供出去，每次引入使用就可以，也避免每次都要cv
>
> 下面就对发布npm包的具体步骤进行示例

第1步：进行注册

在[npm官网进行注册](https://www.npmjs.com)，具体步骤我就不说了，好像有个邮箱激活，记得激活，要不然一直成功不了

![img](https://img-blog.csdn.net/20180528142949904?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3J1bm5pbmdfc2h1YWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第2步：使用命令行进行登录

- 如果为第一次登录使用 `npm adduser`
- 如果已经登陆过就是用`npm login`
- 然后需要你输入`username、password 和 email`，使用你注册的这些进行输入就可以，输入密码的时候没有任何提示，你就输完就可以，他会自动提示你已经建立链接

第3步：创建要发布的文件夹

- 新建文件夹

- npm init

- 根据提示输入内容![img](https://img2018.cnblogs.com/blog/1628733/201909/1628733-20190906163611026-1799640511.png)

- 各选项代表的意思

  ```
  默认字段简介：
  name：发布的包名，默认是上级文件夹名。不得与现在npm中的包名重复。包名不能有大写字母/空格/下滑线!
  version：你这个包的版本，默认是1.0.0。对于npm包的版本号有着一系列的规则，模块的版本号采用X.Y.Z的格式，具体体现为：
    1、修复bug，小改动，增加z。
    2、增加新特性，可向后兼容，增加y
    3、有很大的改动，无法向下兼容,增加x
  description：项目简介
  mian：入口文件，默认是Index.js，可以修改成自己的文件 
  scripts：包含各种脚本执行命令
  test：测试命令。
  author：写自己的账号名
  license：这个直接回车，开源文件协议吧，也可以是MIT，看需要吧。
  ```

- 然后在主入口文件输入自己的一系列方法

第4步：发布 `npm publish`

第5步：删除npm包  `npm unpublish`

第6步：更新npm包  

```
1、本地更新版本号
        比如我想来个1.0.1版本，注意，是最后一位修改了增1，那么命令
        ：npm version patch    回车就可以了；
        比如我想来个1.1.0版本，注意，是第二位修改了增1，那么命令
        ：npm version minor    回车就可以了；
        比如我想来个2.0.0版本，注意，是第一位修改了增1，那么命令
        ：npm version major     回车就可以了；
    2、修改远端的版本,提交到远端npm中：
        npm publish 
```

第7步：常见错误

[参考网址](https://www.jianshu.com/p/6ffa934da70c)

- 还有其他的比如说`timeout`的字样，就说明网不好，可以换个网试试，如果还是不可以可以等待再试试，都会有解决办法。

第8步：安装使用

- 安装 `npm i package（包名）-S`
- 使用就直接在页面中引入就可以 `import {} from package（包名）`