---
title: CSS动画知多少
date: 2018-02-02 23:15:52
categories: css
tags:
     - css
description: CSS动画入门
---
# CSS3动画

### CSS3动画的优势：

	- 运行效果流畅，浏览器会去性能优化
	- 不借助js就能实现动画

### CSS3动画的劣势：

	- 兼容性不足
	- 范围应用不足

## Transition
---
过渡动画，在引入Transition属性之前，CSS是没有时间轴概念的，所有的状态都是瞬时完成。

### Transition属性

1. transition-property：
	
	用来声明transition中会被用到的属性，例如：`transition-property: width;`，如果想设置所有属性可以写all。

2. transition-duration：

	用来声明动画的持续时间，可以是秒s也可以是毫秒ms。例如：

	```css
	div {
	    trasition-duration: 1s;
	}
	
	div {
	    trasition-duration: 100ms;
	}
	```

3. transition-timing-function:
	
	该属性声明了动画的缓动类型，默认值为ease，慢慢加速，到中间减速，最后缓慢到达终点。比较常用的属性有：linear匀速，cubic-bezier贝塞尔曲线

4. transition-delay:

	声明了动画开始的延时时间，例如：

	```css
	div {
	    trasition-delay: 1s;
	}
	```

5. transition简写：

	使用transition属性可以将上述属性值合并到一起，例如：

	```css
	div {
	    transition-property: all;
	    trasition-duration: 1s;
	    transition-timing-function: linear;
	    trasition-delay: 1s;
	}
	
	div {
	    transition: all 1s linear 1s
	}
	```

### 监听事件

`transitionend` 事件会在 CSS transition结束后触发. 当transition完成前移除transition时，比如移除css的`transition-property`属性，事件将不会被触发.如在transition完成前设置  `display` 为`none`，事件同样不会被触发。

示例：

```js
function handleTransition() {
    console.log('Transition 已完成')
}

document.querySelector('dom').addEventListener("transitionend", handleTransition, false)
```

## Animation

---

由于transition需要触发，并且只能执行一次，并不能定义中间状态。于是CSS3又引入了Animation属性。

### Animation属性

1. 定义keyframes:
	
	keyframes是定义的动画名称，里面是动画要产生的效果。例如：

	```css
	@keyframes show {
	    0% {
	        color: red;
	    }
	    50% {
	        color: yellow;
	    }
	    100% {
	        color: green;
	    }
	}
	```

2. animation-name：

	指定动画的名称，也就是keyframes定义的名字。例如：

	```css
	div {
	    animation-name: show;
	}
	```

3. animation-duration:

	动画执行时间，与transition使用方法一样。

4. animation-timing-function:

	动画缓动类型，与transition使用方法一样。

5. animation-delay:

	动画延迟时间，与transition使用方法一样。

6. animation-iteration-count:
	
	指定动画的循环次数，例如：无限循环`animation-iteration-count: infinite`

7. animation-direction:

	定义动画的执行顺序，该属性有如下属性值：

		- normal：默认值，从0%到100%。
		- reverse：从100%到0%。
		- alternate：动画在0%到100%反复执行。
		- alternate-reverse: 动画在100%到0%反复执行。

### 监听事件

1. `animationstart` 事件会在 CSS 动画开始时触发。 如果有 `animation-delay` 延时，事件会在延迟时效过后立即触发。为负数的延时时长会致使事件被触发时事件的 `elapsedTime` 属性值等于该时长的绝对值（并且，相应地，动画将直接播放该时长绝对值之后的动画）。
2. `animationend` 事件会在一个 CSS 动画完成时触发（不包括完成前就已终止的情况，例如元素变得不可见或者动画从元素中移除）。
3. 动画的迭代结束时会触发`animationiteration`事件。`animation-iteration-count`为1的动画不会发生此事件。

## Transform

---

transform属性应用于元素 2D 或 3D 转换。该属性允许我们对元素进行旋转、缩放、移动或倾斜。

### Transform属性值

1. translate3d：

	该值可以将元素在x-y-z平面内做平移，且不会影响到其他元素，还会开启硬件加速。例如：

	```css
	div {
	    transform: translate3d(10px 10px 10px);
	}
	```

2. rotate：

	该属性值可以将元素进行旋转，参数为0deg到360deg，可以将deg理解为旋转多少度。

3. skew： 

	该属性值可以将元素进行扭曲，需要指定x轴和y轴的扭曲角度。

4. matrix：

	该值是所有2d效果的方法总和。


