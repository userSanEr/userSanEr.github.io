---
title: even loop循环机制
date: 2020-07-16 20:00:09
tags: [学习，面试]
categories: 面试
---

本篇主要讲解js的线程，even loop循环机制，堆栈，以及promise进阶面试题

<!-- more -->

##### 1、event loop是什么？

Event Loop 是 JavaScript 异步编程的核心思想。

我们注意到，在异步代码完成后仍有可能要在一旁等待，因为此时程序可能在做其他的事情，等到程序空闲下来才有时间去看哪些异步已经完成了。所以 JavaScript 有一套机制去处理同步和异步操作，那就是事件循环 (Event Loop)。


用文字描述的话，大致是这样的:

- 所有同步任务都在主线程上执行，形成一个执行栈 (Execution Context Stack)。
- 而异步任务会被放置到 Task Table，也就是上图中的异步处理模块，当异步任务有了运行结果，就将该函数移入任务队列。
- 一旦执行栈中的所有同步任务执行完毕，引擎就会读取任务队列，然后将任务队列中的第一个任务压入执行栈中运行。

主线程不断重复第三步，也就是 `只要主线程空了，就会去读取任务队列`，该过程不断重复，这就是所谓的 `事件循环`。

##### 2、为什么 JavaScript 是单线程的？

我们都知道 JavaScript 是一门 `单线程` 语言，也就是说同一时间只能做一件事。这是因为 JavaScript 生来作为浏览器脚本语言，主要用来处理与用户的交互、网络以及操作 DOM。这就决定了它只能是单线程的，否则会带来很复杂的同步问题。

假设 JavaScript 有两个线程，一个线程在某个 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

既然 Javascript 是单线程的，它就像是只有一个窗口的银行，客户不得不排队一个一个的等待办理。同理 JavaScript 的任务也要一个接一个的执行，如果某个任务（比如加载高清图片）是个耗时任务，那浏览器岂不得一直卡着？为了防止主线程的阻塞，JavaScript 有了 `同步` 和 `异步` 的概念。

###### 同步

如果在一个函数返回的时候，调用者就能够得到预期结果，那么这个函数就是同步的。也就是说同步方法调用一旦开始，调用者必须等到该函数调用返回后，才能继续后续的行为。下面这段段代码首先会弹出 alert 框，如果你不点击 `确定` 按钮，所有的页面交互都被锁死，并且后续的 `console` 语句不会被打印出来。

```
alert('Yancey');
console.log('is');
console.log('the');
```

###### 异步

如果在函数返回的时候，调用者还不能够得到预期结果，而是需要在将来通过一定的手段得到，那么这个函数就是异步的。比如说发一个网络请求，我们告诉主程序等到接收到数据后再通知我，然后我们就可以去做其他的事情了。当异步完成后，会通知到我们，但是此时可能程序正在做其他的事情，所以即使异步完成了也需要在一旁等待，等到程序空闲下来才有时间去看哪些异步已经完成了，再去执行。

这也就是定时器并不能精确在指定时间后输出回调函数结果的原因。

```
setTimeout(() => {
  console.log('yancey');
}, 1000);

for (let i = 0; i < 100000000; i += 1) {
  // todo
}
```

##### 3、执行栈和任务队列

###### 执行栈

当我们调用一个方法的时候，JavaScript 会生成一个与这个方法对应的执行环境，又叫执行上下文(context)。这个执行环境中保存着该方法的私有作用域、上层作用域(作用域链)、方法的参数，以及这个作用域中定义的变量和 this 的指向，而当一系列方法被依次调用的时候。由于 JavaScript 是单线程的，这些方法就会按顺序被排列在一个单独的地方，这个地方就是所谓执行栈。

- `执行栈`中的代码永远最先执行

###### 任务队列

事件队列是一个存储着 `异步任务` 的队列，其中的任务严格按照时间先后顺序执行，排在队头的任务将会率先执行，而排在队尾的任务会最后执行。事件队列每次仅执行一个任务，在该任务执行完毕之后，再执行下一个任务。执行栈则是一个类似于函数调用栈的运行容器，当执行栈为空时，JS 引擎便检查事件队列，如果事件队列不为空的话，事件队列便将第一个任务压入执行栈中运行。

- 当`执行栈`中的代码执行完毕，会在执行`宏任务队列`之前先看看`微任务队列`中有没有任务，如果有会先将`微任务队列`中的任务清空才会去执行`宏任务队列`

##### 4、面试题利器

- 在执行时会先进行宏任务，在宏任务如果有`setTimeout、fetch、ajax、事件回调、setInteral，setImmediate`等会加入消息队列，然后查看有没有微任务，eg. `promise、async、await`等就先执行微任务，等微任务执行完之后，在进行消息队列的内容
- `Promise.then()`相当于每次都返回一个`promise`对象，但是要执行的事件要进入异步，加入微任务列表
- `Promise.then()`函数执行中如果存在`setTimeout`，会等待定时器进行完成之后在进入下一个进程`.then`
- 只有`node环境`中会存在`process.nextTick`以及宏任务的`setImmediate`
- 一般情况下`process.nextTick`会先于`promise.then`执行
- `requestAnimationFrame`会`晚于.then`执行，`早于消息队列`内容执行
- `setTimeout、setImmediate`并不能决定哪个优先执行，一般按照压入消息队列的顺序进行执行
- 对于`async、await`的理解，`async`相当于一个promise的`excutor`函数，`同步`执行，`await`相当于`promise的.then函数`，`异步`执行。

```
setTimeout(() => {
    console.log('A');  //压入消息队列 //4、A
}, 0);
var obj = {
    func: function() {
        setTimeout(function() {
        	console.log('B');  //压入消息队列  //5、B
    	}, 0);
        return new Promise(function(resolve) {
            console.log('C');  //1、C
            resolve();
        });
	},
};
obj.func().then(function() {
	console.log('D');  //压入微任务队列 //3、D
});
console.log('E');  //2、E
```

##### 5、面试升级过山车 主要是根据promise进行的微任务分析

###### 得心应手

```
new Promise((resolve,reject)=>{
    console.log("promise1")  //1、promise1
    resolve()
}).then(()=>{  //压入微任务队列 因为之后没有了 
    console.log("then11")  //2、then11
    new Promise((resolve,reject)=>{
        console.log("promise2")  //3、promise2
        resolve()
    }).then(()=>{
        console.log("then21")  //4、then21
    }).then(()=>{
        console.log("then23")  //6、then23
    })
}).then(()=>{
    console.log("then12")  //5、then12
})
```

###### 游刃有余

如果.then里面新的promise是被return出去的就有当这个promise执行完才会走下一个微任务

```
new Promise((resolve,reject)=>{
    console.log("promise1")  //1、promise1
    resolve()
}).then(()=>{
    console.log("then11")  //2、then11
    return new Promise((resolve,reject)=>{
        console.log("promise2")  //3、promise2
        resolve()
	}).then(()=>{
    	console.log("then21")  //4、then21
    }).then(()=>{
    	console.log("then23")  //5、then23
    })
}).then(()=>{
    console.log("then12")  //6、then12 
})
```

###### 炉火纯青

```
new Promise((resolve,reject)=>{
    console.log("promise1")  //1、promise1
    resolve()
}).then(()=>{
    console.log("then11")  //3、then11
    new Promise((resolve,reject)=>{
        console.log("promise2")  //4、promise2
        resolve()
    }).then(()=>{
        console.log("then21")  //6、then21
    }).then(()=>{
        console.log("then23")  //7、then23
    })
}).then(()=>{
    console.log("then12")  //8、then12
})
new Promise((resolve,reject)=>{
    console.log("promise3")  //2、promise3
    resolve()
}).then(()=>{
    console.log("then31") //5、then31
})
```

###### 登峰造极

如果.then里面新的promise是被return出去的就有当这个promise执行完才会走下一个微任务

然后会在另一个新的promise走完3步之后再开始进行这个promise之后的下一个任务

```
new Promise((resolve,reject)=>{
    console.log("promise1")  //1、promise1
    resolve()
}).then(()=>{
	console.log('2')  //3、2
}).then(()=>{
	console.log("then11")  //5、then11
	return new Promise((resolve,reject)=>{
		console.log("promise2")  //6、promise2
		resolve()  
	})
}).then(()=>{
	console.log('3')  //10、3
}).then(()=>{
	console.log('4')  //12、4
})

new Promise((resolve,reject)=>{
	console.log("promise3")  //2、 promise3
	resolve()
}).then(()=>{
	console.log("then31")  //4、then31
}).then(()=>{
	console.log("then33")  //7、then33
}).then(()=>{
	console.log("then34")  //8、then34
}).then(()=>{
	console.log("then35")  //9、then35
}).then(()=>{
	console.log("then37")  //11、then37
}).then(()=>{
	console.log("then39")  //13、then39
})

```

###### 终极变态

这个是把微任务以及宏任务综合的，过了这一关你就离大神又进了一步

```
async function async1() {
    console.log("async1 start");  //2、async1 start
    await  async2();
    console.log("async1 end");  //压入微任务  //7、async1 end
}
async  function async2() {
    console.log( 'async2');  //3、async2
}
console.log("script start");  //1、script start
setTimeout(function () {
    console.log("settimeout");  //压入消息列表  //9、settimeout
});
async1() 
new Promise(function (resolve) {
    console.log("promise1");  //4、promise1
    resolve();
}).then(function () {  //压入微任务  //8、promise2
    console.log("promise2");
});
setImmediate(()=>{  //压入消息列表
    console.log("setImmediate")  //10、setImmediate
})
process.nextTick(()=>{  //压入微任务  //6、process
    console.log("process")
})
console.log('script end');   //5、script end
```

