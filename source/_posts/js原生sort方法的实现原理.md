---
title: js原生sort方法的实现原理
date: 2019-05-11 23:28:01
categories: js
tags: 
    - js
    - sort
---

有没有想过javascript中数组的排序方法Array.prototype.sort()内部是使用什么排序算法实现的呢？

## sort()

关于sort方法的使用就不多说了，很简单：

sort方法可以直接调用，不传入任何参数，也可以传入一个比较函数作为参数

1. 当不传入参数时，sort方法会调用默认的排序函数，即先调用每个数组元素的toString()转型方法，然后按照字符串的Unicode编码顺序来对字符串进行排序。

2. 关于比较函数，函数接受两个参数，若函数返回值大于0，则执行交换，否则不交换

示例代码：

{% codepen brightc GaqOGE dark js,result %}

## sort内部的排序算法

看源码可知，sort内部是快排的实现，但是在数据长度较小时会使用插排，即如果数组长度小于等于22（v8代码逻辑中好像是10）的时候使用插入排序，大于这个值使用快速排序，但是在快速排序递归调用过程中，分治的数组长度小于等于22也会使用插入排序。

```js
    function InnerArraySort(array, length, comparefn) {
    // In-place QuickSort algorithm.
    // For short (length <= 22) arrays, insertion sort is used for efficiency.
```

[chromium v8引擎array.js](https://github.com/v8/v8/blob/ad82a40509c5b5b4680d4299c8f08d6c6d31af3c/src/js/array.js)

***

以上~