---
title: 理解JavaScript中的闭包
date: 2019-06-02 21:57:02
categories: JS实战
tags:
     - JavaScript
description: 理解JavaScript中的闭包概念
---

> JavaScript中闭包的概念一直很难理解，尽管闭包在开发中非常有用，但是其工作原理一直比较抽象。
>
> 我们通过这篇文章向大家介绍下闭包的基础知识以及日常运用。



### 闭包是什么

我们可以把它看作是一块保持变量引用的持久作用域，JavaScript和Ruby都支持闭包。即使在执行了某一代码块之后，闭包允许变量引用它们的父级作用域，只要变量在某处有引用，闭包就一直存在。

来看示例：

```javascript
function test() {
 let obj = getObj(); 

 let result = function() { testCallback(obj); }
}
```

我们来看result函数，它是一个能够访问obj对象的函数。函数result形成了一个闭包 - result函数拥有访问其父作用域元素的能力。

我认为闭包是一种粘性的父环境，当声明变量时附加到变量或执行上下文的引用快照。

### 闭包能做什么

JavaScript是单线程的，并且存在异步执行机制。代码不会发生等待，如果函数内部有函数，则内部函数可以在其整个生命周期内访问其作用域成员。我们经常所写的回调函数就可以成为一个闭包。

我们来看示例：

```javascript
let Alligator = function () {
 let alligatorLength = Math.floor(Math.random()*10);
 let getAlligatorLength = function() { 
  console.log(alligatorLength); 
 };
}
```

`getAlligatorLength`作用域引用了Alligator`函数的变量。

我们可以利用闭包实现函数柯里化，减少调用次数。如下所示：

```javascript
function add(name1) {
  return function(name2) {
    return name1+ " and " + name2;
  }
}

console.log(add("alligator")("croc")); 
```

闭包可以与柯里化一起使用，比如，声明一个类的私有变量。

```javascript
function Obj() {
 let hiddenPower = "I can fly";
 return {
  showPower: () => { console.log(hiddenPower); }
 };
}

let obj = new Obj();
console.log(obj.hiddenPower); // undefined
obj.showPower(); // I can fly
```

### 小结

闭包是JavaScript中一个非常微妙但强大的功能，理解它是成为一个真正JavaScript开发人员的重要一步。

