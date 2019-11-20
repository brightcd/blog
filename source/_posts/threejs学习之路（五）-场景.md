---
title: threejs学习之路（五）-- 场景
date: 2019-11-20 13:19:27
categories: js
tags:
    - webgl
    - threejs
---

前面介绍了相机，相机拍摄的就是场景Scene，场景可以理解为一个舞台（也可以理解为一张画布canvas），我们可以往上面放各种东西，包括物体、灯光等

## 构造一个场景

threejs中创建一个场景非常简单，构造函数Scene()

```js
    var scene = new THREE.Scene()

    var camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 0.1, 1000)
    camera.position.set(5, 5, 5)
    camera.lookAt(new THREE.Vector3(0, 0, 0))

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    var geometry = new THREE.BoxGeometry(1, 1, 1)
    // wireframe为true表示将几何体渲染为线框
    var material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true })
    var cube = new THREE.Mesh(geometry, material)

    scene.add(cube)

    renderer.render(scene, camera)
```

{% asset_img 1.png Markdown Preview Enhanced %}

修改场景的背景色（相当于更改canvas背景色），当然也可以使用纹理

```js
    scene.background = new THREE.Color(0x00ff00)
```

{% asset_img 2.png Markdown Preview Enhanced %}

此外，我们可以随手给场景添加雾化效果

## 雾

场景有一个属性雾fog，可以往场景中添加雾，影响了场景中每一个物体的雾类型

>当然还要看物体的材质是否受雾影响。材质基类Material中属性fog控制材质是否受雾影响，默认为true。

### 线性雾Fog

线性雾的密度是随着距离线性增大的，越远的物体雾越浓密

线性雾构造函数：Fog( color : Integer, near : Float, far : Float )

* color：雾的颜色，如果为黑色，远处的物体将被渲染为黑色
* near：与相机（视点）距离小于near的物体不会被雾影响，默认值1
* far：与相机（视点）距离大于far的物体不会被雾所影响，默认值1000

添加雾化效果

```js
    scene.fog = new THREE.Fog(0xffffff, 7, 10)
```

雾化过程越短（far-near越小），雾化效果越明显

可以这样理解线性雾的密度随距离线性增大，整个可变距离（雾化过程）越小时，则在这段距离内的雾化效果越陡峭，也就越明显。

{% asset_img 3.png Markdown Preview Enhanced %}

### 指数雾FogExp2

它可以在相机附近提供清晰的视野，且距离相机越远，雾的浓度随着指数增长越快。

指数雾构造函数：FogExp2( color : Integer, density : Float )

* color：雾的颜色
* density：定义雾的密度将会增长多快，默认值0.00025

```js
    scene.fog = new THREE.FogExp2(0xffffff, 0.1)
```

{% asset_img 4.png Markdown Preview Enhanced %}

## 总结

以上就是场景的基本知识，当然有一些东西如overrideMaterial，dispose ()等没有介绍到，但是我相信随着学习和理解深入，都会接触到的
