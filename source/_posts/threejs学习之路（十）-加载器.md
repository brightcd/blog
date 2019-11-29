---
title: threejs学习之路（十）-- 加载器
date: 2019-11-25 16:00:12
categories: js
tags:
    - webgl
    - threejs
---

加载器，顾名思义就是加载个什么东西，threejs中的加载器可以加载很多资源，包括图片、字体、纹理、材质等等。

本文简单了解加载器的用法（这里我们要加载资源，第一节里准备的http-server派上用场了）

## Loader

加载器基类，Loader( manager : LoadingManager )，唯一的参数是加载器管理器，其实就是用于管理加载过程的，一般默认的加载管理器就够用了，我们暂时不讨论。

属性：

crossOrigin : string 跨域资源的可用域名
path : String 资源的基本路径
resourcePath : String 外部资源的基本路径

## FileLoader

使用XMLHttpRequest来加载资源的低级类，并由大多数加载器内部使用。 它也可以直接用于加载任何没有对应加载器的文件类型。

>注意: 必须启用缓存THREE.Cache.enabled = true;这是一个全局属性，只需要设置一次，供内部使用FileLoader的所有加载器使用。 Cache 是​​一个缓存模块，用于保存通过此加载器发出的每个请求的响应，因此每个文件都会被请求一次。

FileLoader ( manager : LoadingManager )
manager — loadingManager 是加载器所使用的加载管理器。 默认为 DefaultLoadingManager.

最重要的方法：
load ( url : String, onLoad : Function, onProgress : Function, onError : Function ) : null
url — 文件的URL或者路径，也可以为 Data URI.
onLoad (可选) — 加载完成时将调用。回调参数将是加载的响应。
onProgress (可选) — 将在加载过程中进行调用。参数将是XMLHttpRequest实例， 其中包含 total 和 loaded 字节
onError (可选) — 在加载错误时被调用。

其他一些方法可以设置请求头等http请求参数

```js
    var fileLoader = new THREE.FileLoader()

    fileLoader.load('test.txt', 
        // onLoad
        function (text) {
            console.log('res:', text)
        },
        // onProgress
        function (p) {
            console.log('progress:', p)
        },
        // onError
        function (e) {
            console.log('err:', e)
        }
    )
```

## 内部实现基于FileLoader的加载器

AnimationLoader 以JSON格式来加载 AnimationClips 的一个类。
AudioLoader 用来加载 AudioBuffer的一个类。
BufferGeometryLoader 用来加载BufferGeometry的加载器。
CompressedTextureLoader 基于块的纹理加载器 (dds, pvr, ...)的抽象类。
DataTextureLoader 用于加载二进制文件格式的(rgbe, hdr, ...)的抽象类。
FontLoader 使用JSON格式中加载字体的一个类。返回Font, 返回值是表示字体的Shape类型的数组。
ObjectLoader 用于加载json资源。
MaterialLoader 以JSON格式来加载Material的加载器。

以上这部分加载器的使用跟FileLoader十分相似，都使用load方法加载资源。

## 图片加载器

ImageLoader 用来加载一个Image的加载器。 内部使用FileLoader来加载文件。

## 内部实现基于ImageLoader的加载器

CubeTextureLoader 加载CubeTexture的一个类。
TextureLoader 加载texture的一个类。最常用的加载器，因为我们总要加载各种各样的纹理。

## ImageBitmapLoader

一个把Image加载为ImageBitmap的加载器。 ImageBitmap提供了一种异步且有效的资源的途径，用于在WebGL中渲染的纹理。
不像FileLoader, ImageBitmapLoader无需避免对同一的URL进行多次请求。

## 总结

加载器很简单，就是加载资源用的，简单了解一下加载器，为我们后面学习纹理做准备。
