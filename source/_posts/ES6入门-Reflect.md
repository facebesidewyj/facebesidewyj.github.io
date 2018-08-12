---
title: ES6入门-Reflect
date: 2018-01-12 22:05:12
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门--Reflect
---
# 概述

1. 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上，现阶段，某些方法同时在Object和Reflect对象上部署，未来新的方法只部署在Reflect对象上，也就是说从Reflect对象上可以拿到语言内部的方法。

2. 修改某些Object方法的返回结果，让其变得更为合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj, name, desc)则会返回false。例如：

	```javascript
	try {
	    Object.defineProperty(target, property, attributes);
	} catch (e) {
	
	}
	
	// 新写法
	if (Reflect.defineProperty(target, property, attributes)) {
	    // success
	} else {
	    // false
	}
	```
3. 让Object操作都变成函数行为。某些Object操作时命令式，比如name in obj 或 delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成函数行为。例如：

	```javascript
	 // 老写法
	 'assign' in Object
	
	 // 新写法
	 Reflect.has(Object, 'assign')
	```

4. Reflect对象的方法和Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。这就让Proxy对象可以方便的调用对应的Reflect方法，完成默认行为，作为修改行为的基础。也就是说，不管Proxy怎么修改默认行为，总可以在Reflect上获取默认行为。例如：

	```javascript
	 var loggedObj = new Proxy(obj, {
	     get(target, name) {
	         console.log('get', target, name);
	         return Reflect.get(target, name);
	     },
	     deleteProperty(target, name) {
	         console.log('deleteProperty' + name);
	         return Reflect.deleteProperty(target, name);
	     },
	     has(target, name) {
	         console.log('has' + name);
	         return Reflect.has(target, name);
	     }
	 });
	```

> 每一个Proxy对象的拦截操作，内部都调用了对应的Reflect方法，保证了原生行为能够正常执行，并添加附件方法。

# 静态方法

1. Reflect.get(target, name, receiver) 该方法查找并返回target对象的name属性，如果没有该属性，则返回undefined。例如：


	```javascript
	Reflect.get(obj, 'age');
	```


2. Reflect.set(target, name, value, receiver)该方法设置target对象的name属性等于value。例如：

	```javascript
	Reflect.set(obj, 'age', 18);
	```

3. Reflect.has(obj, name)该方法对应name in obj里面的in运算符。

4. Reflect.deleteProperty(obj, name)该方法等同于delete obj[name]，用于删除对象的属性。

5. Reflect.construct(target, args)该方法等同于new target(...args)，这提供了一种不使用new，来调用构造函数的方法。例如：

	  ```javascript
	  function Greeting(name) {
	    this.name = name;
	  }
	  const instance = Reflect.construct(Greeting, ['jack']);
	  ```

6. Reflect.getPrototypeOf(obj)该方法用于获取__proto__属性，对应Object.getPrototypeOf(obj)。例如：

	  ```javascript
	  Reflect.getPrototypeOf(obj) === obj.prototype;
	  ```

7. Reflect.setPrototypeOf(obj, newProto)该方法用于设置对象的__proto__属性，返回第一个参数对象。例如：

	  ```javascript
	  Reflect.setPrototypeOf(obj, otherObj.prototype);
	  ```

8. Reflect.apply(func, thisArg, args)该方法等同于Function.prototype.apply.call(func, thisArg, args)，用于绑定this对象后执行给定函数。例如：

	  ```javascript
	  const youngest = Reflect.apply(Math.min, Math, ages);
	  ```

9. Reflect.defineProperty(target, propertyKey, attributes)该方法用来为对象定义属性，未来会代替掉Object.defineProperty。例如：

	  ```javascript
	  Reflect.defineProperty(MyDate, 'now', {
	    value: () => Date.now()
	  });
	  ```

10. Reflect.getOwnPropertyDescriptor(target, propertyKey)该方法用于得到指定属性的描述对象。例如：

	  ```javascript
	  var obj = {};
	  Reflect.defineProperty(obj, 'hidden', {
	    value: true,
	    enumerable: false
	  });
	
	  var theDescriptor = Reflect.getOwnPropertyDescriptor(obj, 'hidden');
	  ```

11. Reflect.isExtensible(target)该方法返回一个布尔值，表示当前对象是否可扩展。例如：

	  ```javascript
	  var obj = {};
	  Reflect.isExtensible(obj);
	  ```

12. Reflect.prevenExtensions(target)该方法用于让一个对象变为不可扩展，它返回一个布尔值，表示是否操作成功。例如：

	  ```javascript
	  var obj = {};
	  Reflect.prevenExtensions(obj);
	  ```

13. Reflect.ownKeys(target)该方法用于返回对象的所有属性。例如：

	  ```javascript
	  var obj = {
	    foo: 1,
	    bar: 2
	  }
	
	  // ['foo', 'bar']
	  Reflect.ownKeys(obj);
	  ```



