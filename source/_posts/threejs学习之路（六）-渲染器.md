---
title: threejs学习之路（六）-- 渲染器
date: 2019-11-20 16:44:34
categories: js
tags:
    - webgl
    - threejs
---

渲染器实际上很复杂，但是简单来说渲染器的工作就是将相机捕捉到的场景帧渲染到画布上

threejs支持多种渲染器，CSS2DRenderer、CSS3DRenderer、SVGRenderer，但是最流行也最常用的是WebGLRenderer，基于WebGL和canvas的渲染

如上所述，WebGLRenderer本身也很复杂，背后的渲染原理更复杂，因此本文只是简单介绍一下用法，有机会再深入研究

## 构造一个渲染器

threejs中webGL渲染器构造函数：WebGLRenderer( parameters : Object )

paramters参数可选，是一个对象，现在只需要知道其中有个canvas属性可以指定一个供渲染器绘制其输出的canvas，它和下面的domElement属性对应。 如果没有传这个参数，会创建一个新canvas。

## 渲染器属性

渲染器属性当然也很多，现在只需要了解一个

domElement：一个canvas，渲染器在其上绘制输出。
渲染器的构造函数会自动创建(如果没有传入canvas参数);你需要做的仅仅是像下面这样将它加页面里去:

```js
    document.body.appendChild( renderer.domElement );
```

## 渲染器方法

目前遇到的几个方法

1. render ( scene : Scene, camera : Camera, renderTarget : WebGLRenderTarget, forceClear : Boolean ) : null
用相机(camera)渲染一个场景(scene)
渲染一般是在canvas上完成的，或者是renderTarget(如果有指定)
如果forceClear值是true，那么颜色、深度及模板缓存将会在渲染之前清除，即使渲染器的autoClear属性值是false
即便forceClear设为true, 也可以通过将autoClearColor、autoClearStencil或autoClearDepth属性的值设为false来阻止对应缓存被清除。

2. setPixelRatio ( value : number ) : null
设置设备像素比。通常用于避免HiDPI设备上绘图模糊

3. setSize ( width : Integer, height : Integer, updateStyle : Boolean ) : null
将输出canvas的大小调整为(width, height)并考虑设备像素比，且将视口从(0, 0)开始调整到适合大小 将updateStyle设置为false以阻止对canvas的样式做任何改变。

## 渲染目标（render target）

render target是一个缓冲，就是在这个缓冲中，视频卡为正在后台渲染的场景绘制像素。 它用于不同的效果，例如用于在一个图像显示在屏幕上之前先做一些处理。

一般是在canvas（画布）上完成渲染，但是也可以指定renderTarget，例如一个WebGLRenderTarget

WebGLRenderTarget(width : Number, height : Number, options : Object)

>WebGLRenderTargetCube被CubeCamera(立体相机)作为它的WebGLRenderTarget使用

目前我了解到render target的一个使用场景是特效的制作，渲染场景后可以在图像显示前做后期特效处理（EffectComposer）

## 总结

渲染器是个很麻烦的东西，总之，暂时知道渲染器的作用是将相机捕捉到的场景帧渲染出来就可以了，上述几个方法用于设置画布信息

```js
    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)
```
