---
title: threejs学习之路（十五）-- 控制器
date: 2019-11-29 16:54:02
categories: js
tags:
    - webgl
    - threejs
---

threejs提供多种控制器用于控制场景转换

## 设备方向控制器

DeviceOrientationControls

基于移动设备的旋转方向控制相机方向

## 拖拽控制器

DragControls

场景中的拖拽监听交互

```js
    var controls = new THREE.DragControls( objects, camera, renderer.domElement );

    // add event listener to highlight dragged objects

    controls.addEventListener( 'dragstart', function ( event ) {
        event.object.material.emissive.set( 0xaaaaaa );
    });

    controls.addEventListener( 'dragend', function ( event ) {
        event.object.material.emissive.set( 0x000000 );
    });
```

## FlyControls

FlyControls可以让你在3D空间中无限制的任意变换相机

## FirstPersonControls

FlyControls 的另一种实现

## 轨道控制器

Orbit controls（轨道控制器）可以使得相机围绕目标进行轨道运动。

```js
    var controls = new THREE.OrbitControls( camera, renderer.domElement );

    //controls.update() must be called after any manual changes to the camera's transform
    camera.position.set( 0, 20, 100 );
    controls.update();
```

## PointerLockControls

监听鼠标事件

## TrackballControls

与Orbit controls（轨道控制器）类似，但是相机绕过南北极时，不保持恒定的相机向上矢量

## TransformControls

一个简单的交互模型响应3D对象的变换，与其他控制器不同，它不是针对相机的

## 总结

控制器实际上大多就是控制相机在3D世界中的变换。
