---
title: threejs学习之路（三）-- 正交投影相机
date: 2019-11-19 17:26:30
categories: js
tags:
    - webgl
    - threejs
---

相机是整个threejs中一个很重要的概念，你可以想象成真实的相机或人眼，从不同的位置和角度看相同的场景，得到的画面是不同的。而threejs就是把相机看到的场景画面渲染出来，想象一下一个静止的场景，我们移动相机或调节焦距，看到的画面也是不同的。

threejs中Camera是相机的抽象基类，这个类不被直接调用

## 相机的分类

继承自Camera的相机子类，就是我们具体使用的相机类型

* 立方相机CubeCamera
* 正交相机OrthographicCamera
* 透视相机PerspectiveCamera
* 立体相机StereoCamera

>由于是初学，我们先来看看最常用的正交相机和透视相机

## 正交相机

这种相机采用正交投影模式，无论物体距离相机远或者近，最终渲染图像中物体的大小都保持不变

这种投影对应渲染2D场景或UI非常有用

### 正交投影

对正交投影最通俗易懂的解释就是用一束平行光照射物体得到投影，这个过程就是正交投影，正交投影的结果表现为在3D世界中平行的线条在2D投影中仍然平行，就像数学老师画的那样。

{% asset_img orthographic.png Markdown Preview Enhanced %}

### 正交投影摄像机

正交相机构造函数：OrthographicCamera( left : Number, right : Number, top : Number, bottom : Number, near : Number, far : Number )

{% asset_img ortho.png Markdown Preview Enhanced %}

其中六个参数分别表示正交投影相机拍摄到的六个面的位置，near表示视点（相机位置）与近平面的距离，far表示视点与远平面的距离

上图六个面围成的区域成为视锥体，也叫视景区，我理解只有在视景区内的场景才能被正交相机捕捉到，下面验证一下：

若要保持相机的纵横比例，（right-left)与(top-bottom)的比例应该与canvas宽高比一致

```js
    var scene = new THREE.Scene()

    var camera = new THREE.OrthographicCamera(-window.innerWidth/2, window.innerWidth/2, window.innerHeight/2, -window.innerHeight/2, 100, 2000)
    camera.position.z = 150

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    var geometry = new THREE.BoxGeometry(100, 100, 100)
    var material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true })
    var cube = new THREE.Mesh(geometry, material)

    scene.add(cube)

    renderer.render(scene, camera)
```

{% asset_img 1.png Markdown Preview Enhanced %}

视景区变窄，照相机的视野范围变窄了，导致正方体在视野范围内的横向比例增加了，因此表现为正方体变宽了

```js
    var camera = new THREE.OrthographicCamera(-window.innerWidth/4, window.innerWidth/4, window.innerHeight/2, -window.innerHeight/2, 100, 2000)
```

{% asset_img 2.png Markdown Preview Enhanced %}

改变相机位置，相机向右移动100，物体在视野中向左移动100

```js
    camera.position.set(100, 0, 150)
```

{% asset_img 3.png Markdown Preview Enhanced %}

改变视景体位置，视景体左移，物体左移

```js
    var camera = new THREE.OrthographicCamera(-window.innerWidth/4, window.innerWidth/2, window.innerHeight/2, -window.innerHeight/2, 100, 2000)
```

{% asset_img 4.png Markdown Preview Enhanced %}

超出视景区的内容不可见，正方体中心在原点，与z轴正方向相交在(0, 0, 50)，可视区近面距离视点100，那么相机z方向位置小于150，则正方体前面的面不可见

```js
    camera.position.z = 100
```

{% asset_img 5.png Markdown Preview Enhanced %}

改变视点角度

```js
    camera.position.set(100, 0, 150)
    camera.lookAt(new THREE.Vector3(0, 50, 0))
```

{% asset_img 6.png Markdown Preview Enhanced %}

如结果所示，渲染视图中平行的边依然平行，尝试拉远相机，视图大小并没有改变

## 总结

使用正交投影相机拍摄，渲染视图中平行的边依然平行，尝试拉远相机，视图大小不会改变

仅视景体（相机可视区）内的场景会被正交相机捕捉
