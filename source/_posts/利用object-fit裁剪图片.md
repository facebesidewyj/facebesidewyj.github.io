---
title: 利用object-fit裁剪图片
date: 2019-08-04 21:04:15
categories: css
tags:
  - css
description: CSS动画入门
---

> 如果想要一个图片完全适配某个指定的尺寸的同时，保持其长宽比并且避免图片变形，我们该如何做？ 
>
> 1、使用背景图解决问题
>
> 2、使用图片并且使用`object-fit`裁剪图片

`object-fit`除了`inherit`，`initial`和`unset` 属性之外，还支持以下5个属性：

* `contain`: 图像保持原始长宽比，但会调整其大小，使高度或宽度中的最大值适配指定的尺寸，不会被裁剪。
* `cover`: 图像保持原始长宽比，图像区域完全覆盖，如果图片的长高比与指定尺寸不匹配，图片将被剪裁以适应指定区域。
* `fill`: 默认值。 图像填充指定区域，不保持长宽比，会被拉伸或压缩。
* `none`: 不调整大小，原始图像大小填充指定区域。
* `scale-down`: 图像尺寸与 `none` 或 `contain` 中的一个相同，取决于它们两个之间谁得到的图像尺寸会更小一些。

如下所示， 图像的原始长度为1200px，宽度为674px。 这里显示的尺寸只有它的一半，600px到337px：

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:600px;height:337px"></img>

如果我们需要图像宽度相同，但长度为300px。图像被压缩：

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px"></img>

我们使用`object-fit`来使图片保持长宽比：

### object-fit: contain

```css
img {
  object-fit: contain;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:contain;"></img>

### object-fit: cover

```css
img {
  object-fit: cover;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:cover;"></img>

### object-fit: fill

```css
img {
  object-fit: fill;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:fill;"></img>

### object-fit: none

```css
img {
  object-fit: none;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:none;"></img>

### object-fit: scale-down

```css
img {
  object-fit: scale-down;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:scale-down;"></img>

## object-position

`object-position`可以控制图片的显示位置，我们可以试着改变X轴上图片可见部分的位置，以便让我们看到图片的最右边：

```css
.img {
  object-fit: cover;
  object-position: 100% 0;
  width: 300px;
  height: 337px;
}
```

<img src="https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=f4ec1b60bcb7d0a264c9029dfbed760d/b2de9c82d158ccbf3559c2e817d8bc3eb035417e.jpg" style="width:300px;height:337px;object-fit:cover;object-position:100% 0;"></img>