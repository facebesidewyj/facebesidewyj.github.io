---
title: JavaScript必会算法-排序
date: 2017-11-18 09:07:42
categories: 算法
tags:
     - 算法
     - JavaScript
description: 4个用JavaScript实现的排序算法
---
写在前面的交换函数:
```
/**
 * 交换函数
 * @param  {Array} arr   要交换的数组
 * @param  {Number} num1 传入的第一个值
 * @param  {Number} num2 传入的第二个值
 */
function swap(arr, num1, num2) {
    var temp = arr[num1];
    arr[num1] = arr[num2];
    arr[num2] = temp;
}
```

# 冒泡排序
```
/**
 * 冒泡排序
 * @param  {Array} arr 传入要排序的数组
 * @return {Array}     返回排序好的数组
 */
function bubbleSort(arr) {
    var length = arr.length;
    var flag = true;
    for (var i = 0; i < length && flag; i++) {
        flag = false;
        for (var j = 0; j < length - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);

                // 发生了交换
                flag = true;
            }
        }
    }
    return arr;
}
```

# 选择排序
```
/**
 * 选择排序
 * @param  {Array} arr 传入要排序的数组
 * @return {Array}     返回排序好的数组
 */
function selectionSort(arr) {
    var length = arr.length;
    for (var i = 0; i < length - 1; i++) {
        var minIndex = i;
        for (var j = i + 1; j < length; j++) {
            if (arr[minIndex] > arr[j]) {
                minIndex = j;
            }
        }
        if (minIndex !== i) {
            swap(arr, i, minIndex);
        }
    }
    return arr;
}
```

# 插入排序
```
/**
 * 插入排序
 * @param  {Array} arr 传入要排序的数组
 * @return {Array}     返回排序好的数组
 */
function insertSort(arr) {
    var length = arr.length;
    for (var i = 1; i < length; i++) {
        for (var j = i; j > 0; j--) {
            if (arr[j] < arr[j - 1]) {
                swap(arr, j, j - 1);
            }
        }
    }
    return arr;
}
```

# 快速排序
```
/**
 * 快速排序
 * @param  {Array} arr 传入要排序的数组
 * @return {Array}     返回排序好的数组
 */
function quickSort(arr) {

    // 递归临界条件
    if (arr.length <= 1) {
        return arr;
    }

    // 取出基准点
    var temp = arr.splice(0, 1)[0];
    var leftArr = [];
    var rightArr = [];

    for (var i = 0; i < arr.length; i++) {
        if (temp < arr[i]) {
            rightArr.push(arr[i]);
        } else {
            leftArr.push(arr[i]);
        }
    }
    return quickSort(leftArr).concat([temp], quickSort(rightArr));
}
```
