---
title: threejs学习之路（十三）-- 光源
date: 2019-11-25 17:58:01
categories: js
tags:
    - webgl
    - threejs
---

终于来了一个有意思的东西，光~

有了光，物体和场景才会有层次感，才更接近现实三维世界。

## 光

光本身也是一种3D对象，光源的基类Light继承自3DObject。

Light( color : Integer, intensity : float )

color - (可选参数) 16进制表示光的颜色。 缺省值 0xffffff (白色)。
intensity - (可选参数) 光照强度。 缺省值 1。

>不能直接调用这个基类创造光源

## 环境光

环境光会均匀的照亮场景中的所有物体。环境光不能用来投射阴影，因为它没有方向。

现实中环境光是一种近似的存在，由很多光源漫反射形成，类似你走在光鲜亮丽的商场中，每个方向都有光，把你照得十分明亮，但是却**没有影子**。

AmbientLight( color : Integer, intensity : Float )

color - (参数可选）颜色的rgb数值。缺省值为 0xffffff。
intensity - (参数可选)光照的强度。缺省值为 1。

环境光可以放在任意一个位置，不会衰减，不需要设置光强，各个点都一样，所以不用设置位置。

创建一个几何体：

```js
    var scene = new THREE.Scene()

    var camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 0.1, 1000)
    camera.lookAt(new THREE.Vector3(0, 0, 0))

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    camera.position.set(3, 2, 10)

    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var cube = new THREE.Mesh(geometry, new THREE.MeshBasicMaterial());

    scene.add(cube)

    renderer.render(scene, camera)
```

{% asset_img 1.png Markdown Preview Enhanced %}

给它来点红色环境光：

```js
    var light = new THREE.AmbientLight(0xff0000)
    scene.add(light)
```

结果没有任何反应，为什么呢？之前介绍材质的时候提到过，MeshBasicMaterial这种材质是完全不受光照影响的。只取决于材质原色本身。

{% asset_img 1.png Markdown Preview Enhanced %}

换一种材质MeshLambertMaterial：

```js
    var cube = new THREE.Mesh(geometry, new THREE.MeshLambertMaterial())
```

可以看到白色物体被纯红的环境光照成了红色。

{% asset_img 2.png Markdown Preview Enhanced %}

我们尝试降低光照强度：

```js
    var light = new THREE.AmbientLight(0xff0000, 0.3)
```

可以看到整个物体变暗了，当光照强度为0，则相机只能捕捉到漆黑一片，我们可以配合环境光和其他光源营造各种氛围。

{% asset_img 3.png Markdown Preview Enhanced %}

一个场景中可以同时添加各种强度和颜色的多个环境光，他们共同作用于感光物体（材质）。光源位置则没有影响。

加入蓝光：

```js
    var light = new THREE.AmbientLight(0xff0000, 0.3)
    var light2 = new THREE.AmbientLight(0x0000ff, 0.5)
    scene.add(light, light2)
```

{% asset_img 4.png Markdown Preview Enhanced %}

## 平行光

类似于太阳光

平行光是沿着特定方向发射的光(光源)。这种光的表现像是无限远,从它发出的光线都是平行的。常常用平行光来模拟太阳光 的效果; 太阳足够远，因此我们可以认为太阳的位置是无限远，所以我们认为从太阳发出的光线也都是平行的。

平行光可以投射阴影

构造函数：DirectionalLight( color : Integer, intensity : Float )

属性：

position : Vector3
假如这个值设置等于 Object3D.DefaultUp (0, 1, 0),那么光线将会从上往下照射。

shadow : DirectionalLightShadow
这个 DirectionalLightShadow 对象用来计算该平行光产生的阴影。

target : Object3D
平行光的方向是从它的位置到目标位置。默认的目标位置为原点 (0,0,0)。
>注意: 对于目标的位置，要将其更改为除缺省值之外的任何位置,它必须被添加到 scene 场景中去。

这使得属性target中的 matrixWorld 会每帧自动更新。它也可以设置target为场景中的其他对象(任意拥有 position 属性的对象), 完成上述操作后，平行光现在就可以追踪到目标对像了。

创建一个平行光:

```js
    // 场景背景设为白色，便于观察
    scene.background = new THREE.Color(0xffffff)

    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var cube = new THREE.Mesh(geometry, new THREE.MeshLambertMaterial())

    var light = new THREE.DirectionalLight(0xff0000)
    scene.add(light)
```

可以看到，上面同样的物体，我们只能看到一个上面，这说明了平行光默认是从上方照向原点的。

{% asset_img 5.png Markdown Preview Enhanced %}

改变position，从左上方照入（-1, 1, 1）:

```js
    light.position.set(-1, 1, 1)
```

这时物体左侧和上侧被红光照亮了

{% asset_img 6.png Markdown Preview Enhanced %}

阴影留到后面说，来看看target属性，我们再创建一个物体。

```js
    var cube2 = new THREE.Mesh(geometry, new THREE.MeshLambertMaterial()); 
    cube2.position.set(-5, -2, -5)
    scene.add(cube2)
```

当前状态

{% asset_img 7.png Markdown Preview Enhanced %}

改变target，使得平行光不再照向原点，而是追踪cube2

```js
    light.target = cube2
```

平行光变成了从右前方照向左后方的光线

{% asset_img 8.png Markdown Preview Enhanced %}

>当然，平行光也可以和其他平行光或其他光源共同作用。总之，一个环境中的最终效果总是由这个环境中的所有光源共同作用的结果。

## 聚光灯

聚光灯是从一个方向上的一个点发出，沿着一个圆锥体，它离光越远，它的尺寸就越大。

该光源可以投射阴影。

构造函数：SpotLight( color : Integer, intensity : Float, distance : Float, angle : Radians, penumbra : Float, decay : Float )

distance - 从光源发出光的最大距离，其强度根据光源的距离线性衰减。如果非零，那么光强度将会从最大值当前灯光位置处按照距离线性衰减到0。 缺省值为 0.0

angle - 光线散射角度，最大为Math.PI/2。从聚光灯的位置以弧度表示聚光灯的最大范围。应该不超过 Math.PI/2。默认值为 Math.PI/3

penumbra - 聚光锥的半影衰减百分比。在0和1之间的值。默认为0。

decay - 沿着光照距离的衰减量。在 physically correct 模式下，decay 设置为等于2将实现现实世界的光衰减。缺省值为 1。

power : Float
光功率，在 physically correct 模式中， 表示以"流明（光通量单位）"为单位的光功率。 缺省值 - 4Math.PI。该值与 intensity 直接关联power = intensity * 4π修改该值也会导致光强度的改变。

>position、shadow、target 同平行光

创建一个新的聚光灯：

```js
    var light = new THREE.SpotLight(0xff0000)
    scene.add(light)

    // 稍微调整一下光源位置，便于观察
    light.position.set(-1, 1, 1)
```

{% asset_img 9.png Markdown Preview Enhanced %}

目前光源照向原点，收缩光源的照射距离：

```js
    light.distance = 9
```

由于衰减的原因，远离光源的地方更暗了

{% asset_img 10.png Markdown Preview Enhanced %}

在此基础上，我们减小angle，散射角度，使聚光灯光源（光束）更聚集：

```js
    light.angle = Math.PI/10
```

这时，物体靠边缘的部分已经不能被光线照到了

{% asset_img 11.png Markdown Preview Enhanced %}

调整半影衰减百分比（值越大，光束径向衰减越快，光束更加模糊柔和）和光照距离衰减量（值越大，光照随距离衰减速度越快）：

```js
    light.penumbra = 1
    light.decay = 2
```

这时物体更暗了

{% asset_img 12.png Markdown Preview Enhanced %}

增大光功率：

```js
    light.power = Math.PI * 8
```

power = intensity * 4π，（默认值 4Math.PI） 光照更强了

{% asset_img 13.png Markdown Preview Enhanced %}

>注意一点：如果摄像机在聚光灯光源的”背后”，那么是什么都看不到的。

## 点光源

从一个点向各个方向发射的光源。一个常见的例子是模拟一个灯泡发出的光。

该光源可以投射阴影

构造函数：PointLight( color : Integer, intensity : Float, distance : Number, decay : Float )

distance - 这个距离表示从光源到光照强度为0的位置。 当设置为0时，光永远不会消失(距离无穷大)。缺省值 0.
decay - 沿着光照距离的衰退量。缺省值 1。 在 physically correct 模式中，decay = 2。

>position、power、shadow属性类似聚光灯

创建一个点光源（点光源要配合位置position使用）：

```js
    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var cube = new THREE.Mesh(geometry, new THREE.MeshLambertMaterial())

    var light = new THREE.PointLight(0xff0000)
    light.position.set(-1, 1, 1)

    scene.add(light)
```

{% asset_img 15.png Markdown Preview Enhanced %}

设置衰减距离：

```js
    light.distance = 3
```

{% asset_img 16.png Markdown Preview Enhanced %}

增大随距离的衰减量：

```js
    light.decay = 2
```

{% asset_img 17.png Markdown Preview Enhanced %}

## 半球光

光源直接放置于场景之上，光照颜色从天空光线颜色颜色渐变到地面光线颜色。

半球光不能投射阴影。

构造函数：HemisphereLight( skyColor : Integer, groundColor : Integer, intensity : Float )

skyColor - (可选参数) 天空中发出光线的颜色。 缺省值 0xffffff。
groundColor - (可选参数) 地面发出光线的颜色。 缺省值 0xffffff。
intensity - (可选参数) 光照强度。 缺省值 1。

半球光有position属性，可以调节方向（与平行光类似）

场景中创建一个半球光

```js
    var light = new THREE.HemisphereLight(0xff0000, 0x0000ff, 1)
    scene.add(light)
```

可以理解为上方有红光照下，下方有蓝光照上。

{% asset_img 14.png Markdown Preview Enhanced %}

## 平面光光源（RectAreaLight）

>这个不常用，后面学习

平面光光源从一个矩形平面上均匀地发射光线。这种光源可以用来模拟像明亮的窗户或者条状灯光光源。

## 总结

光源照亮了这个世界，多个光源可以同时使用，有的材质感光，有的材质不感光，有个光源能投射阴影，有的光源不能。
