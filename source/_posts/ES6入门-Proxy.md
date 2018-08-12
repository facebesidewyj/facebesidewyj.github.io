---
title: ES6入门-Proxy
date: 2018-01-28 11:28:40
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门--Proxy
---
# 概述

1. Proxy用于修改某些操作的默认行为，属于一种“元编程”，即对编程语言进行编程。

	Proxy译为代理器，它在目标对象之前架设一层拦截，提供一种机制，来对外界的访问进行过滤和改写。
	
	示例：
		
	```javascript
	var obj = new Proxy({}, {
	    get: function(target, key, receiver) {
	        console.log(`getting${key}`);
	        return Reflect.get(target, key, receiver);
	    },
	    set: function(target, key, value, receiver) {
	        console.log(`setting${key}`);
	        return Reflect.set(target, key.value, receiver);
	    }
	})
	```
	解释：上面代码对空对象架设一层拦截，拦截了get和set方法，当调用对象的get和set方法就会先执行Proxy里的操作。

2. ES6原生提供了Proxy的构造函数，用来生成Proxy实例：

	```javascript
	var proxy = new Proxy(target, handle);
	```
	解释：参数target表示要拦截的目标对象，handle也是一个对象，用来设置拦截行为。该构造函数返回一个对象，即proxy代理对象。

	> 注：要使Proxy起作用，必须针对Proxy实例进行操作，而不是针对目标对象进行操作。如果handle参数没有做任何拦截，则等同于直接通向源对象。

3. 可以将在Object对象里设置proxy属性，直接调用Object.proxy。也可以将对象的原型设置为Proxy对象。
	
	示例：
	```javascript
	var object = {
	    proxy: new Proxy(target, handle)
	};

	// 设置原型对象
	var proxy = new Proxy({},{});
	let obj = Object.create(proxy);
	```

# 实例方法

1. get()
	get()方法用于拦截某个属性的读取操作，接收三个参数，目标对象、属性名和proxy实例本身，最后一个参数可选。
	示例：
	
	```javascript
	var person = {
	    name: 'jack'
	};
	
	var proxy = new Proxy(person, {
	    get: function(target, property) {
	        if (property in target) {
	            return target[property];
	        } else {
	            throw new Error('error');
	        }
	    }
	});
	
	person.name; // jack
	person.age; // error
	```

2. set()

	用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和Proxy实例本身，最后一个参数可选。
	示例：
	
	```javascript
	let validator = {
	    set: function(target, key, value) {
	        if (key === 'age') {
	            if (value > 200) {
	                throw new Error('error');
	            }
	        }
	    }
	}
	var person = new Proxy({}, validator);
	person.age = 500; // error
	```

3. apply()

	apply()方法拦截参数的调用，call和apply操作。该方法接受三个参数，分别是目标对象，上下文对象(this)和目标对象的参数数组。
	示例：

	```javascript
	var handle = {
	    apply: function(target, ctx, args) {
	        return Reflect.apply(...args);
	    }
	}
	```

4. has()

	has()方法用来拦截HasProperty()操作，即判断某个对象是否具有某个属性时，这个方法会生效，典型操作时in运算符。
	示例：

	```javascript
	var handle = {
	    has(target, key) {
	        if (key[0] === '-') {
	            return false;
	        }
	        return key in target;
	    }
	}
	
	let person = {
	    _name: 'jack',
	    age: 19
	};
	
	let proxyPerson = new Proxy(person, handle);
	
	// 检测私有属性
	_name in proxyPerson // false
	```

5. construct()

	construct()方法用于拦截 new 命令。该方法接受两个参数依次为目标对象和目标对象的参数。

	示例：

	```javascript
	var p = new Proxy(function() {}, {
	    construct: function(target, args) {
	        console.log('construct');
	        return {
	            value: args[0] * 10
	        };
	    }
	});
	new p(1).value; // 10
	```

	> 注：construct()方法必须返回一个对象，否则会报错。

6. deleteProperty()

	该方法用于拦截 delete 操作，如果这个方法抛出错误或者返回 false，则当前属性就无法被 delete 命令删除。
	
	示例：

	```javascript
	var proxy = new Proxy({
	    _foo: 'foo'
	}, {
	    deleteProperty: function(target, key) {
	        if (key[0] === '_') {
	            throw new Error('error');
	        }
	        return true;
	    }
	});
	```

	> 注：目标对象自身的不可配置（configurable）的属性，不能被 deleteProperty()删除。

7. defineProperty()

	该方法拦截了 Object.defineProperty()的操作。

8. getOwnPropertyDescriptor()

	该方法拦截了 Object.getOwnPropertyDescriptor()，返回一个属性描述对象或者 undefined。

9. getPrototypeOf()

	该方法用来拦截获取对象原型，getPrototypeOf()方法的返回值必须是对象或者 null，如果目标对象不可扩展，则该方法必须返回原型对象。

10. isExtensible()

	该方法拦截 Object.isExtensible 操作，该方法只能返回布尔值，否则只会被自动转为布尔值。

11. ownKeys()

	该方法用来拦截对象自身的读取操作。

12. preventExtensions()

	该方法拦截 Object.preventExtensions()方法，必须返回一个布尔值。否则会自动转为布尔值。

13. setPrototypeOf()

	该方法拦截 Object.setPrototypeOf()方法，拦截修改原型的方法，只能返回布尔值，否则会自动转为布尔值。

# revocable方法

该方法返回一个可取消的 Proxy 实例。

示例：

```javascript
let target = {};
let handle = {};

let {
    proxy,
    revoke
} = Proxy.revocable(target, handle);

proxy.foo = 123;
proxy.foo; // 123

revoke();
proxy.foo; // error
```

# this问题

Proxy 代理下，目标对象的内部 this 会指向 Proxy 代理。

示例：

```javascript
const target = {
    m: function() {
        console.log(this === proxy);
    }
};
const handle = {};

const proxy = new Proxy(target, handle);

target.m; // false
proxy.m; // true
```

# 实例

```javascript
function createWebService(baseUrl) {
    return new Proxy({}, {
        get(target, key, receiver) {
            return () => httpGet(baseUrl + '/' + key);
        }
    });
}

const service = createWebService('http://example.com/data');
service.employees().then(json => {
    const employees = JSON.parse(json);
});
```
