---
title: ts学习
date: 2020-07-16 20:11:33
tags: [学习，Typescript]
categories: Typescript
---

本篇主要Typescript的基础知识进行了简单总结。

<!-- more -->

### 入手使用

- 下载 `npm install -g typescript `

- 编写代码：按照ts标准进行代码编写

- 编译、运行

  `tsc 文件名.ts`

  `node 文件名.js`

- 注意事项

  - ts默认编译为ES3，如果想要编译到其他版本可在`tsconfig.json`文件中进行配置
  - 但是当配置了该文件之后，再进行编译直接使用`tsc`即可，否则会忽略掉该文件的配置
  - 在使用symbol的时候需要尤其注意，否则会报错

  ```
  {
      "compilerOptions": {
          "target": "es2015",
          "lib":["dom","es2015"]
      }
  }
  ```

#### 1、基本类型

- 支持所有的类型

  ```
  //undefined boolean string object null function
  let b: boolean =  true
  let a: string = 'add'
  ```

- symbol的用法

  ```
  //与es6使用方法完全相同 注意使用tsc编译 否则会有提示
  let c = Symbol()
  ```

#### 2、函数、接口

- 接口

  ```
  //接口可以检查对象或者函数参数是否符合类型 但是记得每个属性要以;结尾
  interface Post{
      title: string;
      author: string;
  }
  ```

- 函数

  ```
  //函数的返回值为空时，则可以将返回值设为void 当有返回值时，可以设置，也可以不设置，ts会自动根据参数计算结果进行设置
  function mu(num1: number,num2: number):void{
      console.log(num1,num2)
  }
  mu( 1,2)
  ```

- 接口函数配合使用

  ```
  function getTitle(post:Post){
       console.log(post.title)
  }
  
  //调用方式1：这种方式调用不会报错
  let post = {
      title: '标题',
      author: '123456',
       authorDtate: '2020-07-09'
  }
  getTitle(post)
  
  //方式二：
  let post:Post = {
      title: '标题',
      author: '123456',
       authorDtate: '2020-07-09'  //严格校验 并没有该参数 会提示错误
  }
  getTitle(post)
  
  //方式三：
  getTitle({
      title: '标题',
      author: '123456',
       authorDtate: '2020-07-09'  //严格校验 并没有该参数 会提示错误
  })
  ```

#### 3、数组、元组

- 基本使用

  ```
  let arr: number[] = [1,2,3]
  ```

- 泛型

  ```
  let array: Array<number> = [1,2,4]
  ```

- 元组

  ```
  let tup: [number,string,boolean] = [12,'12',true]
  ```

#### 4、组合类型

- 多种类型

  ```
  //只能为number/string两种类型
  type NumStr = number | string
  let vv: NumStr = 10
  vv = 'hello'
  ```

- 定义变量结果

  ```
  //nn的值只能为on/off
  let nn: "on" | "off" = "on"
  ```
