---
title: threejs学习之路（十四）-- 阴影
date: 2019-11-29 13:20:20
categories: js
tags:
    - webgl
    - threejs
---

光与影的艺术，有光就要有阴影，才符合我们的现实世界，threejs中用LightShadow添加阴影。

## 阴影基类

构造函数： LightShadow( camera : Camera )

camera - 在光的世界里。这用于生成场景的深度图;从光的角度来看，其他物体背后的物体将处于阴影中。

基类有以下属性：

bias : Float 阴影贴图偏差，在确定曲面是否在阴影中时，从标准化深度添加或减去多少。默认值为0.此处非常小的调整（大约0.0001）可能有助于减少阴影中的伪影

mapSize : Vector2 一个Vector2定义阴影贴图的宽度和高度。

radius : Float 将此值设置为大于1的值将模糊阴影的边缘。较高的值会在阴影中产生不必要的条带效果

开始创建一个阴影之前，我们补充几个知识点：

1. 要在场景中使用阴影贴图，渲染器需要开启shadowMap（它包含阴影贴图的引用）设置：

**shadowMap.enabled : Boolean** 如果设置, 请在场景中使用阴影贴图。 默认是 false
**shadowMap.autoUpdate : Boolean** 启用场景中的阴影自动更新。默认是true，如果不需要动态光照/阴影, 则可以在实例化渲染器时将之设为false
**shadowMap.needsUpdate : Boolean** 当被设为true, 场景中的阴影贴图会在下次render调用时刷新。默认是false，如果你已经禁用了阴影贴图的自动更新(shadowMap.autoUpdate = false), 那么想要在下一次渲染时更新阴影的话就需要将此值设为true
**shadowMap.type : Integer** 定义阴影贴图类型 (未过滤, 关闭部分过滤, 关闭部分双线性过滤)
可选值有
*THREE.BasicShadowMap*：能够给出没有经过过滤的阴影映射 —— 速度最快，但质量最差
*THREE.PCFShadowMap* (默认) ：使用Percentage-Closer Filtering (PCF)算法来过滤阴影映射
*THREE.PCFSoftShadowMap*：使用Percentage-Closer Soft Shadows (PCSS) 算法来过滤阴影映射

```js
    renderer.shandowMap.enabled = true;
```

2. Object3D有阴影相关两个属性

castShadow : Boolean 对象是否被渲染到阴影贴图中。默认值为false。也就是说该对象是否能产生阴影。
receiveShadow : Boolean 材质是否接收阴影。默认值为false。表示阴影是否能投射到该物体上。

## 平行光阴影

与其他阴影类不同，它是使用OrthographicCamera来计算阴影，而不是PerspectiveCamera。这是因为来自DirectionalLight的光线是平行的。

DirectionalLightShadow( )
创建一个新的DirectionalLightShadow，不能直接调用-它是在DirectionalLights内部调用使用

有光才有影，我们创建一束平行光

```js
    // 渲染器开启支持阴影贴图
    renderer.shadowMap.enabled = true;

    var light = new THREE.DirectionalLight( 0xffff00, 1 );
    light.position.set( 0, 1, 0 );
    scene.add( light );

    // 需要设置这个为true才能看到阴影，不知道为什么
    light.castShadow = true;

    // 创建两个物体，他们都能投射阴影
    var sphereGeometry = new THREE.SphereBufferGeometry( 1, 32, 32 );
    var sphereMaterial = new THREE.MeshStandardMaterial( { color: 0xff0000 } );
    var sphere = new THREE.Mesh( sphereGeometry, sphereMaterial );
    sphere.position.set(0, 1, 0)
    sphere.castShadow = true;
    scene.add( sphere );

    var boxGeometry = new THREE.BoxGeometry(1, 1, 1);
    var cube = new THREE.Mesh(boxGeometry, new THREE.MeshStandardMaterial( { color: 0xff0000 } ))
    cube.position.x = 2
    cube.castShadow = true;
    scene.add(cube);

    // 创建一个平面接受阴影投射
    var planeGeometry = new THREE.PlaneGeometry( 20, 20, 30, 30 );
    var planeMaterial = new THREE.MeshStandardMaterial( { color: 0x00ff00 } )
    var plane = new THREE.Mesh( planeGeometry, planeMaterial );
    plane.rotateX(-Math.PI / 3)
    plane.position.set(0, -3, 0)
    // 标明物体可以接受阴影贴图
    plane.receiveShadow = true;
    scene.add( plane );
```

{% asset_img 1.png Markdown Preview Enhanced %}

修改光世界里的相机可视区：

```js
    light.shadow.camera.near = 1;
    light.shadow.camera.far = 500;
```

球体不在视区内，因此没有阴影

{% asset_img 2.png Markdown Preview Enhanced %}

模糊阴影边缘

```js
    light.shadow.radius = 3
```

{% asset_img 3.png Markdown Preview Enhanced %}

再加入一束平行光：

```js
    var light2 = new THREE.DirectionalLight( 0xffff00, 0.5 );
    light2.position.set( 1, 1, 1 );
    light2.castShadow = true;
    scene.add( light2 );
```

产生了新的投影

{% asset_img 4.png Markdown Preview Enhanced %}

## 聚光灯阴影

构造函数创建一个 PerspectiveCamera : PerspectiveCamera 来管理阴影的世界视图

camera : Camera
在光的世界里。这用于生成场景的深度图;从光的角度来看，其他物体背后的物体将处于阴影中。

默认值为PerspectiveCamera，近剪裁平面为0.5。 fov将通过更新方法跟踪拥有SpotLight的角度属性。同样，aspect属性将跟踪mapSize的方面。如果设置了灯光的距离属性，则远剪裁平面将跟踪该值，否则默认为500

创建一个聚光灯阴影

```js
    var light = new THREE.SpotLight(0xffffff)
    light.position.set( -3, 6, -1 );
    light.distance = 20
    light.angle = Math.PI/5;
    light.castShadow = true;
    scene.add( light );

    // 调整阴影贴图大小和光世界相机
    light.shadow.mapSize.width = 512;
    light.shadow.mapSize.height = 512;
    light.shadow.camera.near = 0.5;
    light.shadow.camera.far = 500;
```

{% asset_img 5.png Markdown Preview Enhanced %}

## 点光源阴影

创建一个新的PointLightShadow。该方法不是直接调用的 —— 其在内部由PointLight调用。

一个正上方的点光源投影

```js
    var light = new THREE.PointLight(0xffffff);
    light.position.set( 0, 6, 0 );
    light.castShadow = true;
    scene.add(light);
```

{% asset_img 6.png Markdown Preview Enhanced %}

## 总结

阴影能让我们的场景更真实，大部分光源都能产生阴影，但是需要物体材质、渲染器支持阴影。
