---
title: JavaScript高级程序设计读书笔记-第3章
date: 2017-07-31 21:53:19
categories: JS高程笔记
tags:
     - 笔记
     - JavaScript
description: JavaScript高级程序设计读书笔记--第3章基本概念
---
# 语法
## (1)区分大小写
ECMAScript中的一切(变量、函数名和操作符)都**区分**大小写。
## (2)标识符
标识符：指变量、函数、属性的名字，或是函数的参数。
标识符的书写规则如下：
1. 第一个字符必须是字母、下划线_或美元符号。
2. 其他字符可以是字母、下划线、美元符号或数字。

>**注：标识符推荐驼峰法的命名规则如：newCode，不能把关键字、保留字、true、false、和null作为标识符。** 

## (3)注释
* 单行注释：`//`
* 块级注释：
        /*
        *
        */
## (4)严格模式
启用严格模式:在脚本顶部添加"use strict"。它是一个编译指示，告诉支持的JavaScript引擎切换到严格模式。
## (5)语句
JavaScript有效语句以一个分号(;)结尾。
代码块以{}标识。
注：**建议不要省略分号！**。
# 关键字和保留字
* 具有特定用途的关键字：break do instanceof typeof case else new var catch finally return void continue for switch while debugger function this with default if throw delete in try
* 不能用作标识符的保留字：abstract enum int short boolean export interface static byte extends long super char final native synchronized class float package throws const goto private transient debugger implements protected volatile double import pbulic let yield
注：**使用关键字做标识符会导致"Identifier Expected"错误**。
# 变量
ECMAScript中的变量是松散类型的，所谓松散类型就是可以用来保存任何类型的数据。
ECMAScript中使用var来声明变量，未经初始化的变量会保存一个特殊的值undefined。
使用var声明的变量，是定义该变量作用域中的局部变量，也就是说在函数中使用var定义一个变量，在函数退出后，该变量就会销毁。如果不使用var定义变量(不推荐)，将会定义一个全局变量。
# 数据类型
## (1)typeof操作符
typeof是用来检测给定变量的数据类型的。
使用typeof操作符返回的字符串有如下几种：
* "undefined"--未定义
* "boolean"--布尔值
* "string"--字符串
* "number"--数值
* "object"--对象或null
* "function"--函数
例子：
        var message = "aaa";
        alert(typeof(message));
        alert(typeof 1);
## (2)Undefined类型
Undefined类型只有一个值，就是undefined。使用var声明变量时未对其进行初始化，这个变量的值就是undefined。

>注：**我们在声明变量时应尽量对其进行初始化，这样当我们遇到undefined时，就知道该变量是没有被声明，而不是没有被初始化。** 

## (3)Null类型
Null类型也只有一个值，就是null。逻辑上来说null表示一个空对象指针，所以typeof(null)会返回Object。

>注：**null == undefined，但null与undefined的用途不同，我们在声明对象时，应该把没有明确对象的变量值设为null。** 

## (4)Boolean类型
Boolean类型有两个值：true和false。true不一定等于1，false也不一定等于0。
运用转型函数Boolean()可以将一个值转换成对应的Boolean值。
例如:
        var message = "hello world";
        alert(Boolean(message));
各种数据类型转换规则一览表：
<table> <tr> <th>数据类型</th> <th>转换为true的值</th> <th>转换为false的值</th></tr> <tr> <th>Boolean</th> <th>true</th> <th>false</th> </tr> <tr> <th>Number</th> <th>非0数值</th> <th>0和NaN</th> </tr> <tr> <th>String</th> <th>非空字符串</th> <th>""</th> </tr> <tr> <th>Object</th> <th>任何对象</th> <th>null</th> </tr> <tr> <th>Undefined</th> <th> </th> <th>undefined</th> </tr> <table>
## (5)Number类型
### 1.浮点数值
浮点数，即数值中必须有一个小数点，并且小数点后面必须有一位数字。
对于那些极大或极小的浮点数，可以使用e表示法，如:
` var floatNum = 3.12e7; //等于31200000`

>注：**保存浮点数的内存空间是保存整数内存空间的2倍。** 

### 2.数值范围
Number.MIN_VALUE里保存着最小的数值，Number.MAX_VALUE里保存着最大的数值。
如果某次计算结果超出了JavaScript的数值范围，则这个数值将自动转换为Infinity值。
Infinity值无法再次进行计算。
### 3.NaN
NaN：即非数值(Not aNumber)，NaN与任何数值都不相等，包括NaN本身。
ECMAScript定义了一个叫isNaN()的函数，可以用来检测一个参数是不是“非数值”，例如：
        alert(isNaN("blue"));//true
        alert(isNaN(10));//false
### 4.数值转换
三个方法：
&emsp;&emsp;Number(任何类型):可以用于任何类型，
&emsp;&emsp;parseInt(value,进制数):专门用于转换字符串，进制数可以不写，方法会自
&emsp;&emsp;己判断。
&emsp;&emsp;parseFloat(字符串,进制数):专门用于转换字符串，进制数可以不写，方法
&emsp;&emsp;会自己判断。
## (6)String类型
### 1.字符字面量
字符字面量又叫转义序列，用于表示非打印字符，也就是我们平时说的转义字符。常用字符字面量有如下几个：
&emsp;&emsp;\n 换行
&emsp;&emsp;\r 回车
&emsp;&emsp;\\\ 斜杠
&emsp;&emsp;\' 单引号
&emsp;&emsp;\" 双引号
&emsp;&emsp;\b 退格
### 2.字符串特点
ECMAScript中的字符串是不可变的，一旦创建就不能改变，要改变字符串的值，就需要把之前的字符串销毁，在重新定义一个字符串。
注：字符串拼接效率低下的原因就是因为需要销毁原来的字符串，在重新定义字符串。
### 3.转换为字符串
字符串转换的方法有两种：
&emsp;&emsp;toString()：几乎每个值都有的方法，obj.toString(进制数);进制数不传默
&emsp;&emsp;认为十进制。
&emsp;&emsp;String(value)：转型函数，value为任何类型，如果value有toString()方法
&emsp;&emsp;则会执行toString()方法，并返回结果，如果value是null和undefined，则
&emsp;&emsp;返回"null"和"undefined"。
## (7)Object类型
Object实例具有的属性和方法：
&emsp;&emsp;constructor：构造函数，保存着用于创建当前对象的函数。
&emsp;&emsp;hasOwnProperty(propertyName)：检查属性是否在实例中(并不是在实例的原
&emsp;&emsp;型中)。必须传入字符串。
&emsp;&emsp;isPrototypeOf(object)：检查传入的对象是否是当前对象的原型。
&emsp;&emsp;propertyIsEnumerable(propertyName)：检查给定的属性是否能使用for-in
&emsp;&emsp;语句来进行枚举。必须传入字符串。
&emsp;&emsp;toLocaleString()：返回对象的字符串表示，字符串与浏览器的地区对应。
&emsp;&emsp;toString():返回对象的字符串表示。
&emsp;&emsp;valueOf()：返回对象的字符串、数值或布尔值，通常与toString()的返回值
&emsp;&emsp;相等。
# 操作符
## (1)一元操作符
递增和递减操作符：++和--。
一元加和减操作符：+和-，加号放在数值前，没有任何影响，减号常用来表示负数，例如：-1。
## (2)位操作符
## (3)布尔操作符
逻辑非：由！表示，返回一个布尔值。
逻辑与：由&&表示，有两个操作数，属于短路操作。只要一个数为false，则返回false，
两个操作数都为true，才返回true。
逻辑或：由||表示，有两个操作数，也属于短路操作。只要一个数为true，则返回true，
两个数都为false，才返回false。
注：我们可以利用逻辑或来为变量赋值，避免变量被赋值为null或undefined，例如：
`var obj = preObj || backObj;`
## (4)乘性操作符
乘法：由*表示，用于计算两个数的乘积。如果乘积超过了数值表示范围，则返回Infinity。
除法：由/表示，执行第二个操作数除第一个操作数的计算，如果商超过了数值表示范围，
也返回Infinity。
## (5)加性操作符
加法：由+表示，用于加法计算，还可用于字符串拼接和转换字符串。每个加法操作都是独立运行的，举例说明ECMAScript中一个常见的错误：
        var num1 = 1;
        var num2 = 2;
        var str = "str" + num1 + num2; // 结果为str12
        // 正确写法为
        var str2 = "str" + (num1 + num2); // 结果为str3
减法：由-表示，用于减法操作。
## (6)关系操作符
<、>、<=、>=这些是关系操作符，它们会返回一个布尔值，比较规则如下：
* 两个数都是数值，则数值比较。
* 两个数都是字符串，比较字符编码。
* 一个是数值，会将另一个数转换为数值，执行比较。
* 如果有对象，则会先调用valueOf()方法或toString()方法将对象转换，在再进行比较。

>**注：任何操作数与NaN相比较都返回false。** 

## (7)相等操作符
相等和不相等：由==和!=表示，它们会对操作数先进行强制类型转换，再进行比较。不推荐使用。
全等和不全等：由===和!==表示，不进行强制类型转换，直接比较，推荐使用。
## (8)条件操作符
形如：variable = boolean_expression ? true_value : false_value;
本质上，variable的值取决于boolean_expression表达式的值，如果为true，则取true_value，反之，则取false_value。
## (9)赋值操作符
由=表示，用于把等号右边的值赋值给等号左边的变量。
## (10)逗号操作符
使用逗号操作符可以在一条语句中执行多个操作，形如`var num1 = 1, num2 = 2;`
# 语句
## (1)if语句
形如:
```
        if(condition){
            statement
        }
```
## (2)do-while语句
后检测循环语句，先执行后测试条件，形如：
```
        do{
            statement
        }while(expression);
```
## (3)while语句
前循环语句，先检测条件再执行，形如：
```
        while(expression){
            statement
        }
```
## (4)for语句
前循环语句，形如：
```
        for(var i = 0; i < 10; i++){
            statement
        }
```

>注:**循环变量i在循环外部也能访问到。** 

## (5)for-in语句
精准迭代语句，可用来枚举对象，形如：
```
        for(property in expression){
            statement
        }
```
## (7)break和continue语句
break是停止循环，continue是立即退出循环，但重新进行。
## (8)with语句
将代码的作用域设置到一个特定的对象中，形如：
```
        with(expression){
            statement
        }
```
## (9)switch语句
在进行多个数比较时，使用switch，switch使用的是全等操作符，并且可以在语句中使用任何数据类型，每个case可以是常量、表达式和变量，形如：
```
        switch(expression){
            case value: statement
                break;
            case value: statement
                break;
            case value: statement
                break;
            default:statement
        }
```
# 函数
使用事项：要么让函数始终都有返回值，要么永远没有返回值。
## (1)理解参数
参数在ECMAScript中是用一个数组来表示的，ECMAScript不介意传递多少参数，也不在乎参数是什么类型，假设有一个函数有两个传递参数，那么在调用时可以传一个、两个或者不传都没事。命名的参数只是提供便利，但不是必须的。
实际上在函数体内部可以通过arguments对象来访问参数数组，例如：arguments[1]获取第二个参数。

>注：** ECMAScript中所有的参数传递的都是值，不能通过引用传递参数。传递对象时
，是传递对象中的内存地址。** 

## (2)没有重载
ECMAScript函数是不能重载的，后定义的函数会覆盖前面定义的。