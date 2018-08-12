---
title: JavaScript高级程序设计读书笔记-第6章
date: 2017-10-09 22:40:39
categories: JS高程笔记
tags:
     - 笔记
     - JavaScript
description: JavaScript高级程序设计读书笔记--第6章面向对象的程序设计
---
# 理解对象
ECMA-262把对象定义为**无序**属性的集合，其属性可以包含基本值、对象或者函数。
*使用对象字面量创建对象：
```
var person = {
    name: "jack",
    age: 19,
    sayName: function(){
        alert(this.name);
    }
}
```
## 属性类型
1. 数据属性：包含一个数据值的位置，具有4个特性：
 (1)[[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为访问器属性，默认值为true。**configurable一旦被定义为不可配置，就不能把它变回可配置了，此时只能调用Object.defineProperty()方法修改writable特性**。
 (2)[[Enumerable]]：表示能否通过for-in遍历属性，默认值为true。
 (3)[[Writable]]：表示能否修改属性，默认值true。
 (4)[[Value]]：包含这个属性的数据值，默认为undefined。
修改属性默认特性的方法：Object.defineProperty()，接收三个参数：属性所在对象、属性名和一个描述符对象(必须为属性特性名中的一个)。例如：
```
var person = {};
Object.defineProperty(person, "name", {
    writable: false,
    value: "jack"
});
person.name = "aaa";
console.log(person.name); // jack
```

2. 访问器属性：访问器属性不包含数据值，它包含一对儿getter和setter方法(非必须)，具有4个特性：
 (1)[[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为数据属性，默认为true。
 (2)[[Enumerable]]：表示能否通过for-in循环返回属性，默认为true。
 (3)[[Get]]：读取属性时调用的函数，默认为undefined。
 (4)[[Set]]：写入属性时调用的函数，默认为undefined。
访问器属性不能直接定义，必须使用Obeject.defineProperty()来定义，例如：
```
var book = {
    _year: 2004, // 下划线代表私有属性
    edition: 1
}

// 定义访问器属性year
Object.defineProperty(book, "year", {
    get: function(){
        return this._year;
    },
    set: function(newValue){
        if(newValue > 2004){
            this._year = newValue;
            this.edition = newValue - 2004;
        }
    }
});

book.year = 2005;
console.log(book.edition); // 2
```

>注：支持Object.defineProperty()这个方法的有浏览器有IE9+、Firefox4+、Safari5+、Opera12+和Chrome，在不支持该方法的浏览器中不能修改[[Configurable]]和[[Enumerable]]。

## 定义多个属性
Object.defineProperties()可以一次定义多个属性，该方法接收两个参数：第一个参数是要添加或修改属性的对象，第二个是要添加或修改的属性，例如：
```
var book = {};
Obeject.defineProperties(book, {
    _year: {
        writable: true,
        value: 200
    },
    edition: {
        writable: true,
        value: 1
    },
    year: {
        get: function(){
            return this._year;
        },
        set: function(newValue){
            if(newValue > 2004){
                this._year = newValue;
                this.edition = newValue - 2004;
            }
        }
    }
});
```

## 读取属性的特性
Object.getOwnPropertyDescriptor()可以取得给定属性的描述符，该方法接收两个参数：第一个为属性所在对象，第二个为属性名称，返回值是一个对象，例如：
```
var book = {};
Obeject.defineProperties(book, {
    _year: {
        writable: true,
        value: 200
    },
    edition: {
        writable: true,
        value: 1
    },
    year: {
        get: function(){
            return this._year;
        },
        set: function(newValue){
            if(newValue > 2004){
                this._year = newValue;
                this.edition = newValue - 2004;
            }
        }
    }
});

var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
console.log(descriptor.value); // 2004
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // "undefined"
```

# 创建对象
## 工厂模式
工厂模式抽象了创建具体对象的过程，由于ECMAScript无法创建类，所以我们用函数来封装接口，如下所示：
```
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}

var person1 = createPerson("jack", 18, "teacher");
```

## 构造函数模式
构造函数的函数名应该大写，如下所示：
```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };
}
var person1 = new Person("jack", 10, "teacher");
```
构造函数与工厂模式的函数的区别在于：没有显示的创建对象，没有return和直接将属性和方法赋值给了this对象。
可以通过对象constructor属性来获取该对象的构造函数，例如：
```
console.log(person1.construtor == Person); // true
```
说明：
1. 构造函数也是函数，区别只在于它们的调用方式不同，任何函数只要通过new操作符来调用，那么它就可以是构造函数。例如：
```
Person("jack", 19, "teacher"); // 添加到全局作用域中
window.sayName(); // jack
```
2. 构造函数的主要问题是每个方法都要在每个实例上重新创建一遍。解决方法为：我们可以把构造函数中的方法移到构造函数外部，如下所示：
```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName(){
    alert(this.name);
}
var person1 = new Person("jack", 10, "teacher");
```

>注：把方法移到构造函数外部会破坏自定义引用类型的封装性，使其暴露在全局作用域中，我们可以通过下面的原型模式来解决。

## 原型模式
每个函数都有一个prototype属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。
示例：
```
function Person(){
}

Person.prototype.name = "jack";
Person.prototype.age = 29;
Person.protptype.job = "teacher";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
person1.sayName(); // jack

var person2 = new Person();
person2.sayName(); // jack

```
1.理解原型对象
原型对象中有constructor属性，该属性是一个指针，指向prototype属性所在函数，也就是构造函数。
根据构造函数创建的实例中也有一个叫[[Prototype]]的指针，FireFox、Safari和Chrome中以_proto_体现，该指针指向构造函数的原型对象。
构造函数中有prototype属性，是一个指针，指向该函数的原型对象。

**实例与构造函数没有直接的关系，是通过原型对象连接起来的。**

ECMAScript5中新增了一个Object.getPrototypeOf()方法，用来获得实例中[[Protptype]]的值，也就是原型对象。支持这个方法的浏览器有IE9+、Firefox3.5+、Safari5+、Opera12+和chrome。
例如：
```
alter(Object.getPrototypeof(person1).name); // "jack"
```
属性的搜索顺序是先搜索实例，再搜索原型。虽然可以通过对象实例来访问原型中属性的值，但是不能通过对象实例重写原型中属性的值。如果我们在实例中添加了一个与实例原型中的同名属性，则该属性会屏蔽原型中那个属性。例如：
```
function Person(){
}
Person.prototype.name = "jack";
var person = new Person();
person.name = "rose";
console.log(person.name); // "rose"
```
delete操作符能删除实例属性，例如：
```
delete person.name;
console.log(person.name); // "jack"来自原型
```
hasOwnProperty()方法可以检测一个属性是存在于实例中，还是原型中。给定属性存在实例中，才会返回true，例如：
```
person.name = "rose";
console.log(person.hasOwnProperty("name")); // true
var person2 = new Person();
console.log(person.hasOwnProperty("name"))； // false 属性存在原型中
```

2.原型对象与in操作符
(1)单独使用：对象能够访问给定属性时返回true，无论该属性存在于实例中还是原型中。
  例如：
```
function Person() {}

Person.prototype.name = "jack";

var person1 = new Person();
var person2 = new Person();

console.log(person1.hasOwnProperty("name")); // false
console.log("name" in person1); // true
```
自定义一个方法来判断属性到底存在于对象中，还是原型中:
```
function hasPrototypeProperty(obj, name) {
    return !object.hasOwnProperty(name) && (name in obj);
}
```

(2)在for-in中使用：不论在实例中还是原型中，可枚举的属性都能通过for-in循环返回。
Object.keys()方法接收一个对象作为参数，返回可枚举属性的字符串数组。例如：
```
function Person() {}

Person.prototype.name = "jack";

var keys = Object.keys(Person.prototype);
console.log(keys); // name
```
ObjectgetOwnPropertyNames()方法会返回所有属性，无论它是否可枚举。例如：
```
var keys = Object.getOwnPropertyNames(Person.prototype);
console.log(keys); // constructor name
```

3.更简单的原型语法
用一个包含所有属性和方法的对象字面量来重写整个原型对象，例如：
```
function Person() {}

Person.prototype = {
    name: 'jack',
    age: 18,
    sayName: function() {
        alert(this.name);
    }
}
```

这里有一个问题，constructor属性不再指向Person，变成了新对象constructor的属性。例如：
```
var friend = new Person();

// false
console.log(friend.constructor === Person);

// true
console.log(friend.constructor === Object)
```

如果我们想要重设constructor属性，可以这样做：
```
function Person() {}

Person.prototype = {
    constructor: Person,
    name: 'jack',
    age: 18,
    sayName: function() {
        alert(this.name);
    }
}
```

4.原型的动态性
原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能立即从实例上反映出来。

5.原生对象的原型
所有的原生引用类型都在其构造函数的原型上定义了方法，我们也可以通过原型定义新的方法，但这样做的不推荐的。

6.原型对象的问题
原型模式最大问题是它的共享性。对于包含引用类型的原型来说，较容易产生问题，例如：
```
function Person() {}

Person.prototype = {
    constructor: Person,
    name: 'jack',
    age: 18,
    friends: ['rose', 'tom'],
    sayName: function() {
        alert(this.name);
    }
}

var person1 = new Person();
var person2 = new Person();

person1.friends.push('lily');

// rose tom lily
console.log(person2.friends);
```

## 组合使用构造函数模式和原型模式  

构造函数和原型的组合模式是创建对象中最常见的方式，构造函数用于定义实例属性，原型对象用于定义方法和共享的属性。如下所示： 


```javascript
function Person(name) {
    this.name = name;
}
Person.prototype = {
    constructor: Person,
    sayName: function() {
        console.log(this.name);
    }
}
```
## 动态原型模式  

将所有的信息都封装在了构造函数中，通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。如下所示： 

```javascript
function Person(name) {
    this.name = name;
    if (type this.sayName !== 'function') {
        Person.prototype.sayName = function() {
            console.log(this.name);
        }
    }
}
```
## 寄生构造函数模式  

在构造函数内生成一个对象并返回，与工厂模式类似，但该对象与构造函数的原型之间没有任何联系，不推荐使用。 

## 稳妥构造函数模式  

与寄生构造函数模式类似，但是不使用new操作符。

# 继承
## 原型链
## 借用构造函数
## 组合继承
## 原型式继承
## 寄生式继承
## 寄生组合式继承
