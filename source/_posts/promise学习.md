---
title: promise学习
date: 2020-07-16 20:14:30
tags: [学习，ES6]
categories: ES6
---

本篇主要是对promise进行基础学习

<!-- more -->

##### 1、promise是什么？

- 语法上：promise是一个构造函数
- 功能上：promise对象用来封装一个异步操作并可以获取结果

##### 2、promise的状态

pending状态变为resolved

pending状态变为rejected

- 一个promise状态只能改变一次
- 无论成功还是失败都会有一个结果数据
- 成功一般为value，失败一般为reason

##### 3、基本使用

```
const p = new Promise((resolve,reject)=>{
    setTimeout(function(){
        const date = Date.now()
        if(date%2 == 0){
            resolve('成功,time'+date)
        }else{
            reject('失败,time'+date)
        } 
    },1000)
})
p.then(
    value =>{
   		console.log(value)  //指的是成功状态resolve输出的值
    },
    reason =>{
    	console.log(reason)  //指的是失败状态reject输出的值
    }
)
```

##### 4、promise的优点

- 指定回调函数的方式更加灵活：

  - 旧的：必须在启动异步任务前指定
  - promise：启动异步任务 ===>返回promise对象 ===>给promise对象绑定回调函数(甚至可以在异步返回之后)

- 支持链式调用，可以解决回调地狱问题

  - 回调地狱：嵌套调用，外部回调函数异步执行的结果是嵌套的回调函数执行的条件

  - 回调地狱的缺点：不便于阅读/不便于异常处理

  - 解决方案：promise链式调用

    - 从上到下读写方便
    - 直接使用，catch抛出异常

  - 终极解决方案：async/await调用，解决回调更优美

    ```
    //此处为伪代码不可执行
    async function async1(){
        try{
            const result = await dosth()
            const result1 = await dosth(result)
            console.log('port the final result'+finalResult)
        }catch(err){
            console.log(err)
        }
    }
    ```

    

##### 5、promise API==>语法

- Promise构造函数：promise(excutor){}

  - excutor函数：同步执行(resolve,reject)=>{}
  - resolve()函数：内部定义成功时我们调用的函数 value=>{}
  - reject()函数：内部定义失败时我们调用的函数 reson =>{}

  说明：excutor会在promise内部立即同步回调，异步操作在执行器中执行

- promise.prototype.then方法：(onResolved,onRejected)=>{}

  - onResolved函数：成功时我们调用的函数 value=>{}
  - onRejected函数：失败时我们调用的函数 reson =>{}

  说明：指定用于得到成功value的成功回调和用于得到失败reason的失败回调，返回一个新的promise对象

  <table><tr><td bgcolor=#D1EEEE>.then是原型上的方法 </td></tr></table>

- promise.prototype.catch方法:(onRejected)=>{}

  - onRejected函数：失败的回调函数 reson =>{}

  说明：then的语法糖，相当于then(undefined,onReject)

- Promise.reject/Promise.resolve方法：

  说明：返回一个promise对象

  ```
  Promise.reject('2')
  Promise.resolve('3')
  相当于
  new Promise((resolve,reject) =>{
      resolve('3')
      reject('2')
  })
  ```

- Promise.all方法： Promise.all([p1,p3]).then()

  - Promise.all数组中可以包含n个promise对象

  说明：返回一个新的promise对象，只要有一个失败则状态为失败，当都成功时，则返回状态为成功，如果数组内容都成功则返回一个数组，数组的返回顺序与入参的顺序相对应

  ```
   const p1 = new Promise((resolve,reject)=>{
      setTimeout(function(){
       	resolve('1')
   	})
   })
   const p2 = Promise.reject('2')
   const p3 = Promise.resolve('3')
  Promise.all([p1,p3]).then(
      value =>{
      	console.log(value)
      },
      reason =>{
      	console.log(reason)
      }
  )
  ```

- Promise.race方法：Promise.race([p1,p2,p3]).then()

  - Promise.race数组中可以包含n个promise对象
  - 返回一个新的promise，第一个完成的promise状态就是最终的结果状态，如果没有加延迟执行，则传入数组的第一个promise优先执行则结果就为第一个状态

  ```
   const p1 = new Promise((resolve,reject)=>{
      setTimeout(function(){
       	resolve('1')
   	})
   })
   const p2 = Promise.reject('2')
   const p3 = Promise.resolve('3')
  Promise.race([p1,p2,p3]).then(
      value =>{
      	console.log(value)
  	},
      reason =>{
          console.log(reason)
      }
  )
  ```


