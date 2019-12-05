---
title: threejs学习之路（十九）- TWEEN
date: 2019-12-05 10:46:31
categories: js
tags:
    - threejs
    - tweenjs
---

本文介绍一个补间动画库tweenjs的基本用法，配合threejs可以实现很多动画效果。

## tweenjs

动画效果就是使一个对象在一定时间内从一个状态到另外一个状态

```js
    // position = {x: 1}
    var tween = new TWEEN.Tween(position)
    .delay(100) // 等待100ms
    .to({x: 200}, 1000) // 1s时间，x到200
    .onUpdate(render) // 变更期间执行render方法
    .onComplete(() => {
        // 动画完成
    })
    .onStop(() => {
        // 动画停止
    })
    .start(); // 开启动画
```

基本的使用就如上面的例子，此外还有其他的API，如stop、pause等，可以直接看TWEENJS源码。

要让动画真正动起来，我们一般在requestAnimationFrame中调用update方法：

```js
    TWEEN.update()
```

## 缓动类型

tweenjs最强大的地方在于提供了很多常用的缓动动画类型，由api easing()指定，tweenjs本质就是一系列缓动函数算法，因此可以结合canvas、threejs很简单就能实现很多效果。

## 链式调用

tweenjs支持链式调用，如在动画A结束后要执行动画B，我们可以：

```js
    tween1.chain(tween2)
```

利用链式调用我们可以创建往复来回循环的动画：

```js
    var tween1 = new TWEEN.Tween(position).to({x: 200}, 1000);
    var tween2 = new TWEEN.Tween(position).to({x: 0}, 1000);

    tween1.chain(tween2);
    tween2.chain(tween1);

    tween1.start();
```

## 参考

[张鑫旭大神的文章](https://www.zhangxinxu.com/wordpress/2016/12/how-use-tween-js-animation-easing/)
