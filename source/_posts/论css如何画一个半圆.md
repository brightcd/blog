---
title: 论css如何画一个半圆
date: 2019-05-11 23:28:34
categories: css
tags:
---

如何使用css画一个半圆呢？介绍三种方法

## border-radius

boder-radius属性也支持单边设置弧度，不同的弧度可以绘制不同的图形，今天来画一个半圆

直接上代码：

{% codepen brightc GaqyoQ dark css,result %}

>border-radius的值顺序是左上、右上、右下、左下顺时针，盒子的宽高要求是1:2或者2:1，border-radius值不能是百分比，应该与短边一致

## clip

css3中的clip属性可以剪裁绝对定位元素，画半圆的思想就是先画一个圆，然后用clip剪切掉一半

看代码：

{% codepen brightc YbWYGr dark css,result %}

>clip唯一合法的形状值是：rect (top, right, bottom, left)

## 线性渐变

思想还是先画一个圆，然后利用背景线性渐变展示一半

代码：

{% codepen brightc arZEpm dark css,result %}

***

以上，有其他方法再补充~