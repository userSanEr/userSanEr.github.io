---
title: 解读js原型
date: 2020-07-16 19:52:59
tags: [学习，面试]
categories: 面试
---

本篇主要是对原型链进行一下剖析，使大家可以更方便理解

<!-- more -->

#### 1、理解原型链

我们可以将原型区分为原型(prototype)、链(`__proto__`)

##### 1.1 原型(prototype)

原型是一个普通的对象，它为构造函数的实例共享了属性和方法。在所有的实例中，引用到的原型都是同一个对象。

例如：

```
function Student(name){
    this.name = name;
    this.study = function(){  //在这里将方法挂在每个对象上，所以当每次新创建实例时，就会新创建一个方法
    	console.log('study.js')  
    }
}
const student1 = new Student('xiaoming')
const student2 = new Student('xiaohong')
student1.study()
student2.study()
```

我们可以看出2个student中的study方法都是独立的，虽然功能相同，但是在系统中占用的是两份内存，如果我们创建了100个实例，就得占用100份内存，这样算下去，将会造成大量的内存浪费。

所以出现了js出现了prototype

```
 function Student(name){
 	this.name = name;
 }
 Student.prototype.study = function(){
 	console.log('study.js')
 }
 const student1 = new Student('xiaoming')
 const student2 = new Student('xiaohong')
 student1.study()
 student2.study()
```

在使用prototype之后，study方法存放在Student的原型中，只占用一份内存，所有的实例都会共享它，内存问题就解决了

>  然后接着分析原型问题：为什么实例可以访问到原型上的方法？
>
>  答案就是`__proto__`可以通过链访问到原型

##### 1.2 链

链`__proto__`可以理解为一个指针，它是实例对象中的一个属性，指向了构造函数的原型prototype

例如：

```
function Student(name){
	this.name = name;
}
Student.prototype.study = function(){
	console.log('study.js')
}
const student1 = new Student('xiaoming')
const student2 = new Student('xiaohong')
student1.study()
console.log(student1.__proto__ === Student.prototype)  //true  函数实例的__proto__指向了构造函数的prototype
student2.study()
console.log(student1.__proto__ === Student.prototype)  //true  函数实例的__proto__指向了构造函数的prototype
```

> 为什么调用student.study时，访问到的却是Student.prototype.study呢？
>
> 答案在原型链中。

##### 1.3 原型链

原型链指的是：一个实例对象，在调用对象和方法时，会一次从实例本身、构造函数原型、构造函数原型的原型...去寻找，查看是否有对应的属性或方法。

从实例对象，一直找到Object.prototype,专业上称之为原型链

例如：

```
function Student(name){
	this.name = name;
}
Student.prototype.study = function(){
	console.log('study.js')
}
const student = new Student('xiaoming')
student.study()  //study.js
//在这里student往上走以及则为构造函数原型
console.log(student.__proto__.study === Student.prototype.study)  //true

console.log(student.toString())  //"[object Object]"
//在这里student往上走以一级则为构造函数原型  再往上走一级则为Object.prototype
console.log(student.__proto__.__proto__.toString === Object.prototype.toString)
```

> 为什么`Student.prototype.__proto__`是指向Object.prototype?

1、先找到`__proto__`前面的对象，也就是Student.prototype的构造函数。

- 判断Student.prototype类型，typeof student.prototype是object
- object的构造函数是Object
- 得出student.prototype的构造函数是Object

2、所以`Student.prototype.__proto__`是Object.prototype

##### 1.4 原型链常见问题

> `Function.__proto__`是什么？

1、先找到`Function`的构造函数。

- 判断Function类型，typeof Function是function
- 函数类型的构造函数是Function
- 得出Function的构造函数是Function

2、所以`Function.__proto__`=Function.prototype

> `Number.__proto__`是什么？

1、先找到`Number`的构造函数。

- 判断Number类型，typeof Number是function
- 函数类型的构造函数是Function
- 得出Number的构造函数是Function

2、所以`Number.__proto__`=Function.prototype

> `Object.prototype.__proto__`是什么？

为null，防止无限循环


#### 2、深入原型链

##### v8是怎么创建对象的

js代码在执行时，会被V8引擎解析，这是V8引擎会用不同的模块来处理js中的对象和函数

例如：

-  ObjectTemplate用来创建对象
-  FunctionTemplate用来创建函数
-  PrototypeTemplate用来创建函数原型

细品一下 V8 中的定义，我们可以得到以下结论。

- **Js 中的函数**都是 FunctionTemplate 创建出来的，返回值的是 **FunctionTemplate 实例**。
- **Js 中的对象**都是 ObjectTemplate 创建出来的，返回值的是 **ObjectTemplate 实例**。
- **Js 中函数的原型**（prototype）都是通过 PrototypeTemplate 创建出来的，返回值是 **ObjectTemplate 实例**。

所以 Js 中的对象的原型可以这样判断：

- 所有的对象的原型都是 Object.prototype，自定义构造函数的实例除外。
- 自定义构造函数的实例，它的原型是对应的构造函数原型。

在 Js 中的函数原型判断就更加简单了。

- 所有的函数原型，都是 Function.prototype。

下图展示了所有的内置构造函数，他们的原型都是 Function.prototype。

![img](https://user-gold-cdn.xitu.io/2020/7/9/17333de1b0b0f873?imageslim)

##### V8中的函数解析案例

```
function Student(name) {
  this.name = name;
}
Student.prototype.study = function () {
  console.log("study js");
};
const student = new Student('xiaoming')
```

这段代码在 V8 中会这样执行：

```
// 创建一个函数
v8::Local<v8::FunctionTemplate> Student = v8::FunctionTemplate::New();
// 获取函数原型
v8::Local<v8::Template> proto_Student = Student->PrototypeTemplate();
// 设置原型上的方法
proto_Student->Set("study", v8::FunctionTemplate::New(InvokeCallback));
// 获取函数实例
v8::Local<v8::ObjectTemplate> instance_Student = Student->InstanceTemplate();
// 设置实例的属性
instance_Student->Set("name", String::New('xiaoming'));
// 返回构造函数
v8::Local<v8::Function> function = Student->GetFunction();
// 返回构造函数实例
v8::Local<v8::Object> instance = function->NewInstance();
```

以上代码可以分为 4 个步骤：

-  创建函数模板
-  在函数模板中，拿到函数原型并赋值
-  在函数模板中，拿到函数实例并赋值
-  返回构造函数
-  返回构造函数实例
