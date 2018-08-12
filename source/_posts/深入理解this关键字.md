---
title: 深入理解this关键字
date: 2018-02-10 08:56:00
categories: JS实战
tags:
     - JavaScript
description: 谈谈对this关键字的认识
---
# 关于this

1. this关键字是JavaScript中最复杂的机制之一，它是一个很特别的关键字，被**自动**定义到所有的函数作用域中。
2. this提供了一种更优雅的的方式来"隐式"传递一个对象的引用，因此可以将API设计的更加简洁易用。
3. this在任何情况下都不指向函数的词法作用域，也不指向函数自身。
4. this实际上是在函数被调用时发生绑定，它指向什么完全取决于函数在哪里被调用。
5. this 总是指向调用它的那个对象，如果没有明确调用对象，则指向 window，在 node 环境中指向 Global 对象。

> 注：在严格模式下，this 禁止指向 window 对象

# 绑定规则

1. 默认绑定

    函数不带任何修饰的函数引用进行调用的，只能是默认绑定，非严格模式下this指向window对象，严格模式下为undefined，例如：

	```javascript
	var x = 1;
	
	function test() {
	    this.x = 2;
	}
	test();
	console.log(x); // 2
	```

2. 隐式绑定

    当函数的调用有调用对象时，this会根据对象属性引用链的最后一层来决定指向哪个对象，例如：
	```javascript
	function test() {
	    console.log(this.x)
	}
	var obj = {};
	obj.x = 1;
	obj.fun = test;
	obj.fun(); // 1
	```

3. 显式绑定

    JavaScript提供绝的大多数函数，以及自己创建的函数都可以使用call()和apply()方法进行显式绑定，该方法的第一个参数是一个对象，给this准备的。例如：
	```javascript
	 var x = 0;
	
	 function test() {
	     console.log(this.x);
	 }
	 var obj = {};
	 obj.x = 3;
	 obj.fun = test;
	
	 obj.fun().apply(); // 0 没有参数默认指向window对象
	 obj.fun().apply(obj); // 3 指定了this指向了obj
	```

4. new关键字绑定

    通过构造函数通过new关键字生成一个新对象，这时 this 指向该新对象。例如：

	```javascript
	var x = 0;
	
	function Test() {
	    this.x = 3;
	}
	
	var obj = new Test();
	console.log(x); // 0 全局对象的值根本没变
	console.log(obj.x); // 3
	```

> 注：绑定优先级：new>显式绑定>隐式绑定>默认绑定

# 绑定例外

当把null或者undefined作为this的绑定对象传入call或apply方法中，这些值在调用时会被忽略，this应用默认绑定规则。

# 箭头函数

箭头函数中的this并不遵守上述4个绑定规则，而是根据外层的作用域来决定this的绑定的。例如：

```javascript
function foo() {
    setTimeout(() => {
        console.log(this.a);
    }, 100);
}
var bar = {
    a: 2
};
foo.call(bar); // 2
```

