---
title: 玩转JS函数节流和函数防抖
date: 2018-06-30 23:04:19
categories: JS实战
tags:
     - JavaScript
description: 理解函数节流和函数防抖
---
# 函数防抖

## 定义

事件在触发后的n秒执行回调，如果在n秒内多次触发回调函数，则回调函数只执行一次。
例如：有人进入电梯，电梯会在一定5秒后启动，如果又有人进入电梯，则电梯需要再等5秒才启动

> 事件多次触发只执行一次

## 适用场景

1. 防止多次提交
2. 监听滚动事件，触发回调
3. 监听输入框输入，执行对应方法时

## 示例

```javascript
  /**
   * 防抖函数
   * @param  {Function} func  要执行防抖的函数
   * @param  {Number} delay   延迟时间
   * @return {Function}       执行防抖函数的函数
   */
  debounce(func, delay) {
      let timer = null;
      return function(...args) {
          if (timer) {
              clearTimeout(timer);
          }
          timer = setTimeout(() => {
              func.apply(this, args);
          }, delay);
      };
  }
```

# 函数节流

## 定义

函数在单位时间内只能触发一次，如果在单位时间内多次触发，只能生效一次。
例如：动画的播放，一秒钟只播放24张图片

> 大量事件按一定平均频率触发

## 适用场景

1. 页面刷新频率
2. Dom元素拖拽
3. Canvas动画

## 示例

```javascript
/**
 * 节流函数
 * @param  {Function} func  要执行节流的函数
 * @param  {Number} gapTime 间隔时间
 * @return {Function}       执行节流函数的函数
 */
function throttle(fun, gapTime) {
    let _lastTime = null;

    return function() {
        let _nowTime = +new Date()
        if (_nowTime - _lastTime > gapTime || !_lastTime) {
            fun();
            _lastTime = _nowTime
        }
    }
}
```

