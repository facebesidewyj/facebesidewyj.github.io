---
title: css常见问题总结
date: 2017-10-17 21:30:12
categories: css
tags:
     - css
     - 填坑经验
description: 记录在生产环境下遇到的css问题
---
# css遮罩层的实现
```
.shadeBox {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: #aaa;
    opacity: 0.7;
    display: none;
    z-index: 99;
}
```
解释：
1. 要铺满整个页面    top: 0;bottom: 0;left: 0;right: 0;
2. 透明度和颜色设置
3. 图层在最上面z-index:99;
4. 随着页面滚动position:fixed

# 使一个div随另一个div的高度变化而变化
两个div的父元素设置：overflow:hidden;
需要变化的子元素设置：padding-bottom: 1000px;margin-bottom: -1000px;

# CSS行内元素的特征
诸如像span、a等这些行内元素，使用margin-top、margin-bottom、padding-top、padding-bottom、width、height等是无效的，只能设置left和right。如果想让这些属性生效，要添加display:inline-block。
display:inline-block:使元素成为行内块元素。

# CSS属性pointer-events
设置pointer-events:none; 时元素永远不会成为鼠标事件的target。但是，当其后代元素的pointer-events属性指定其他值时，鼠标事件可以指向后代元素，在这种情况下，鼠标事件将在捕获或冒泡阶触发父元素的事件侦听器。

# CSS设置内容溢出显示省略号
```
/* 行内容溢出显示省略号 */
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;

/* 多行显示省略号 */
display:-webkit-box; // 弹性盒子
-webkit-box-orient:vertical // 从上到下垂直排列子元素
-webkit-line-clamp:3 // 显示行数
overflow:hidden // 超出部分隐藏
```
# CSSflex布局
http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html
# CSS伪类active的使用
在WebAPP中为列表和需要点击的Dom对象添加active伪类，使其在点击时能产生高亮效果，接近原生APP体验。

# 适配手机dpi的1像素边框
基于伪类+缩放的实现(基于stylus的写法)
伪类：
```
border-1px($color)
    position: relative
    &:after
        position: absolute
        display: block
        left: 0
        bottom: 0
        width: 100%
        border-top: 1px solid $color
        content: ' '
```
缩放：
```
@media (-webkit-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)
    .border-1px
        &::after
            // 根据Y轴来定义缩放
            -webkit-transform: scaleY(0.7)
            transform: scaleY(0.7)

@media (-webkit-min-device-pixel-ratio: 2),(min-device-pixel-ratio: 2)
    .border-1px
        &::after
            // 根据Y轴来定义缩放
            -webkit-transform: scaleY(0.5)
            transform: scaleY(0.5)
```

# 两个行内元素之间去掉空白间隙
因为两个元素之间空字符导致的，父盒子font-size设置为0即可，获知两个元素之间不用回车换行。

# 清除浮动的理解
https://juejin.im/post/59e7190bf265da4307025d91

# 模糊半透明背景图的设置
父盒子position为relative
背景图设置：
```
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(10px); // 图片模糊设置
```

# 经典的CSS sticky footer布局
Sticky Footer，完美的绝对底部https://aotu.io/notes/2017/04/13/Sticky-footer/index.html
兼容性最好的实现：
将内容区用一个容器包起来，需要定位在底部的元素与容器平级，就像这样:
```
<div>
    <div class="wrapper clearfix">
        <div class="content"></div>
    </div>
    <div class="footer"></div>
</div>
```
样式如下：
```
.wrapper{
    min-height: 100%; // 需要设置最小高度
}
.clearfix{
    display: inline-block;
}
// 设置伪类来清除浮动
.clearfix: after{
    display: block
    content: '.'
    height: 0
    line-height: 0
    clear: both
    visibility: hidden
}
.wrapper .content{
    padding-bottom: 64px; // 为footer留出位置
}
.footer{
    position: relative;
    margin: -64px auto 0 auto;
    clear: both; // 清除浮动
}
```

# 图片加载失败显示默认图片
