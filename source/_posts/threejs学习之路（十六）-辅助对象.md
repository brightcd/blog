---
title: threejs学习之路（十六）-- 辅助对象
date: 2019-11-30 18:34:11
categories: js
tags:
    - webgl
    - threejs
---

threejs提供了很多有用的辅助工具用来帮助我们创建3D场景。

## 箭头对象

ArrowHelper(dir : Vector3, origin : Vector3, length : Number, hex : Number, headLength : Number, headWidth : Number )

dir -- 基于箭头原点的方向. 必须为单位向量.
origin -- 箭头的原点.
length -- 箭头的长度. 默认为 1.
hex -- 定义的16进制颜色值. 默认为 0xffff00.

headLength -- 箭头头部(锥体)的长度. 默认为箭头长度的0.2倍(0.2 * length).
headWidth -- 箭头的宽度. 默认为箭头头部(锥体)长度的0.2倍(0.2 * headLength).

```js
    // 单位向量
    var dir = new THREE.Vector3(2, 3, 0)
    dir.normalize()

    // 创建一个箭头辅助对象
    var arrow = new THREE.ArrowHelper(dir, new THREE.Vector3(0,0,0), 2, 0xff0000, 1, 1)
    scene.add(arrow)
```

{% asset_img 1.png Markdown Preview Enhanced %}

## 坐标对象

用于简单模拟3个坐标轴的对象.红色代表 X 轴. 绿色代表 Y 轴. 蓝色代表 Z 轴.

AxesHelper( size : Number )

size -- (可选的) 表示代表轴的线段长度. 默认为 1.

```js
    camera.position.set(2, 1, 8)
    var axes = new THREE.AxesHelper(1)
    scene.add(axes)
```

{% asset_img 2.png Markdown Preview Enhanced %}

## BoxHelper

用于图形化地展示对象世界轴心对齐的包围盒的辅助对象。注意:要想能正常运行,目标对象必须包含 Geometry 或 BufferGeometry , 所以当目标对象是精灵 Sprites 时将不能正常运行.

BoxHelper( object : Object3D, color : Color )

object -- (可选的) 被展示世界轴心对齐的包围盒的对象.
color -- (可选的) 线框盒子的16进制颜色值. 默认为 0xffff00.

```js
    var geometry = new THREE.BoxGeometry(1,1,1);
    var material = new THREE.MeshBasicMaterial();
    var box = new THREE.Mesh(geometry, material);
    scene.add(box)

    var boxHelper = new THREE.BoxHelper(box, 0xff0000)
    scene.add(boxHelper)
```

{% asset_img 3.png Markdown Preview Enhanced %}

## CameraHelper

用于模拟相机视锥体的辅助对象.它使用 LineSegments 来模拟相机视锥体.

CameraHelper( camera : Camera )
camera -- 被模拟的相机.

为指定相机创建一个新的相机辅助对象 CameraHelper .

```js
    var cameraHelper = new THREE.CameraHelper(camera)
    scene.add(cameraHelper)
```

{% asset_img 4.png Markdown Preview Enhanced %}

## DirectionalLightHelper

用于模拟场景中平行光 DirectionalLight 的辅助对象. 其中包含了表示光位置的平面和表示光方向的线段.

DirectionalLightHelper( light : DirectionalLight, size : Number, color : Hex )

light-- 被模拟的光源.
size -- (可选的) 平面的尺寸. 默认为 1.
color -- (可选的) 如果没有设置颜色将使用光源的颜色.

```js
    var directionalLight = new THREE.DirectionalLight();
    var directionalLightHelper = new THREE.DirectionalLightHelper(directionalLight, 1, 0x00ff00);
    scene.add(directionalLightHelper)
```

{% asset_img 5.png Markdown Preview Enhanced %}

## GridHelper

坐标格辅助对象. 坐标格实际上是2维线数组.

GridHelper( size : number, divisions : Number, colorCenterLine : Color, colorGrid : Color )

size -- 坐标格尺寸. 默认为 10.
divisions -- 坐标格细分次数. 默认为 10.
colorCenterLine -- 中线颜色. 值可以为 Color 类型, 16进制 和 CSS 颜色名. 默认为 0x444444
colorGrid -- 坐标格网格线颜色. 值可以为 Color 类型, 16进制 和 CSS 颜色名. 默认为 0x888888

创建一个尺寸为 'size' 和 每个维度细分 'divisions' 次的坐标格. 颜色可选.

```js
    var grid = new THREE.GridHelper(10, 10, 0xff0000);
    scene.add(grid);
```

{% asset_img 6.png Markdown Preview Enhanced %}

## PolarGridHelper

极坐标格辅助对象. 坐标格实际上是2维线数组.

PolarGridHelper( radius : Number, radials : Number, circles : Number, divisions : Number, color1 : Color, color2 : Color )
radius -- 极坐标格半径. 可以为任何正数. 默认为 10.
radials -- 径向辐射线数量. 可以为任何正整数. 默认为 16.
circles -- 圆圈的数量. 可以为任何正整数. 默认为 8.
divisions -- 圆圈细分段数. 可以为任何大于或等于3的正整数. 默认为 64.
color1 -- 极坐标格使用的第一个颜色. 值可以为 Color 类型, 16进制 和 CSS 颜色名. 默认为 0x444444
color2 -- 极坐标格使用的第二个颜色. 值可以为 Color 类型, 16进制 和 CSS 颜色名. 默认为 0x888888

创建一个半径为'radius' 包含 'radials' 条径向辐射线 和 'circles' 个细分成 'divisions' 段的圆圈的极坐标格辅助对象. 颜色可选.

```js
    var polarGrid = new THREE.PolarGridHelper();
    scene.add(polarGrid)
```

{% asset_img 7.png Markdown Preview Enhanced %}

## HemisphereLightHelper

创建一个虚拟的球形网格 Mesh 的辅助对象来模拟 半球形光源 HemisphereLight.

HemisphereLightHelper( light : HemisphereLight, sphereSize : Number, color : Hex )

light -- 被模拟的光源.
size -- 用于模拟光源的网格尺寸.
color -- (可选的) 如果没有赋值辅助对象将使用光源的颜色.

```js
    var light = new THREE.HemisphereLight( 0xffffbb, 0xffffff, 1 );
    var helper = new THREE.HemisphereLightHelper( light, 5 );
    scene.add( helper );
```

{% asset_img 8.png Markdown Preview Enhanced %}

## PlaneHelper

用于模拟平面 Plane 的辅助对象.

PlaneHelper( plane : Plane, size : Float, hex : Color )
plane -- 被模拟的平面.
size -- (可选的) 辅助对象的单边长度. 默认为 1.
color -- (可选的) 辅助对象的颜色. 默认为 0xffff00.

先看下平面，在三维空间中无限延伸的二维平面，平面方程用单位长度的法向量和常数表示为海塞法向量Hessian normal form形式。

Plane( normal : Vector3, constant : Float )
normal - (可选参数) 定义单位长度的平面法向量Vector3。默认值为 (1, 0, 0)。
constant - (可选参数) 从原点到平面的有符号距离。 默认值为 0.

创建一个线框辅助对象来表示指定平面.

```js
    var plane = new THREE.Plane( new THREE.Vector3( 1, 1, 0.2 ), 3 );
    var helper = new THREE.PlaneHelper( plane, 1, 0xffff00 );
    scene.add( helper );
```

{% asset_img 9.png Markdown Preview Enhanced %}

## PointLightHelper

创建一个虚拟的球形网格 Mesh 的辅助对象来模拟 点光源 PointLight.

PointLightHelper( light : PointLight, sphereSize : Float, color : Hex )

light -- 要模拟的光源.
sphereSize -- (可选的) 球形辅助对象的尺寸. 默认为 1.
color -- (可选的) 如果没有赋值辅助对象将使用光源的颜色.

```js
    var pointLight = new THREE.PointLight( 0xff0000, 1, 100 );
    pointLight.position.set( 10, 10, 10 );
    scene.add( pointLight );

    var sphereSize = 1;
    var pointLightHelper = new THREE.PointLightHelper( pointLight, sphereSize );
    scene.add( pointLightHelper );
```

{% asset_img 10.png Markdown Preview Enhanced %}

## SpotLightHelper

用于模拟聚光灯 SpotLight 的锥形辅助对象.

SpotLightHelper( light : SpotLight, color : Hex )

light -- 被模拟的聚光灯 SpotLight .
color -- (可选的) 如果没有赋值辅助对象将使用光源的颜色.

```js
    var spotLight = new THREE.SpotLight( 0xffffff );
    spotLight.position.set( 10, 10, 10 );
    scene.add( spotLight );

    var spotLightHelper = new THREE.SpotLightHelper( spotLight );
    scene.add( spotLightHelper );
```

{% asset_img 11.png Markdown Preview Enhanced %}

## 总结

利用这些辅助对象可以帮助我们更好的理解和布局我们的3D世界。

至此，threejs中的大部分内容我们都接触到了，还有一些基础对象和数学库在用到的时候会学习，接下来开始研究一些示例代码并尝试自己写一些东西。
