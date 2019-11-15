---
title: Deractor修饰器
date: 2019-11-15 10:50:02
categories: js
tags: deractor
---

Deractor是ES7的一个提案，可以用来修饰类和类的属性和方法，实际上也可以修饰任何对象属性，修饰器的作用主要有两个：

* 多个类之间共享方法
* 编译期对类和方法进行修改

## 语法

Deractor就是一个函数，这个函数可以用来修饰类，也可以用来修饰类的属性和方法，如果用来修饰类，则这个函数只能接收一个参数，这个参数就是修饰的类本身；如果用来修饰属性或方式，则可以接收三个参数，target表示类本身，key表示修饰的属性，descriptor表示属性描述符

## 修饰类

```js
function deractor (target) {
    target.a = 1
}

@deractor
class A {

}
```

当然也可以修饰类的原型

```js
function de (target) {
    target.prototype.cc = 1
}

@de
class B {

}
```

## 修饰类的属性和方法

修饰类的属性

```js
// 尝试修改属性，控制台会报错
function readOnly (target, key, descriptor) {
    console.log(target, key, descriptor)
    descriptor.writable = false
    return descriptor
}

class C {
    @readOnly
    status = 1
}
```

修饰类的方法

```js
function log (target, key, descriptor) {
    const fn = descriptor.value
    descriptor.value = function (...args) {
        console.log('记录日志：方法执行前')
        fn.apply(this, args)
        console.log('记录日志：方法执行后')
    }
}

function log1 () {
    console.log('记录日志1')
}

class D {
    @log
    test () {

    }

    @log1
    test1 () {

    }
}
```

扩展使用修饰器

```js
function log (msg) {
    return function (target) {
        console.log(msg)
    }
}

@log('E类')
class E {

}

@log('F类')
class F {

}
```

## 总结

以上就是javascript中deractor的基本用法，与传统的装饰器模式效果一样，但是提供一种简洁的语法，我们可以用它抽象出一些共享逻辑，优化架构

如有错漏，欢迎指正~
