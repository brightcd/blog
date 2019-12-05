---
title: threejs学习之路（十七）- 渲染性能监视
date: 2019-12-04 15:16:27
categories: js
tags:
    - threejs
    - webgl
    - stats
---

本文简单介绍一个监控渲染性能数据的库stats.js，可以用来测试webgl代码的渲染性能

## 引入脚本

```html
    <!-- cnd地址：https://www.bootcdn.cn/stats.js/ -->
    <script src="https://cdn.bootcss.com/stats.js/r17/Stats.js"></script>
```

模块

```js
    // 模块
    import { default as Stats } from 'https://cdn.bootcss.com/stats.js/r17/Stats.min.js';
```

## 构造函数

```js
    var stats = new Stats()
```

构造函数会默认创建一个div元素，在页面左上角用于显示渲染时间。

```js
    // 把元素挂载到页面中
    document.body.appendChild(stats.domElement);
```

## setMode(mode)

可以通过stats.setMode(mode)设置mode值，改变首次打开页面时的显示模式，默认mode为0

0: FPS模式，刷新频率，GPU每秒渲染次数
1: MS模式，刷新周期，一次渲染的时间

```js
    stats.setMode(1);
```

## update方法

在requestAnimationFrame循环中调用update，来刷新时间

```js
    stats.update();
```

## 总结

stats源码非常简单，提供的方法除了上面提到的，还有begin、end和addPanel、showPanel，还有一个静态方法Panel用于自定义展现面板，总之stats是一个用来帮助我们测试页面渲染性能的工具。
