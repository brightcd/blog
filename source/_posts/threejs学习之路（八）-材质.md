---
title: threejs学习之路（八）-- 材质
date: 2019-11-23 16:19:40
categories: js
tags: 
    - webgl
    - threejs
---

材质描述了物体的外观，举个简单的例子纸的表面是粗糙的，玻璃的表面是光滑的，这就是因为他们材质不同。

我们现实生活中看到一个物体，比如一本书，首先看到它是一个长方体，然后看到它是纸质材料，我们大概就知道这是一本书了，因此实际上几何形状+材质差不多就能描述一个物体了（当然不同条件下，比如光照不同物体表现在摄像机或人眼中也不同）

学习了几何体，学习了材质，我们是不是就可以绘制各种物体了呢？

## 材质基类Material

>一般情况下材质独立于渲染器

构造函数：Material() 该方法创建一个通用材质。

下面介绍一些基础的材质属性：

fog : Boolean
材质是否受雾影响。默认为true。这属性我们在将场景雾的时候讲过。

side : Integer
定义将要渲染哪一面 - 正面，背面或两者。 默认为THREE.FrontSide。其他选项有THREE.BackSide和THREE.DoubleSide。

transparent : Boolean
定义此材质是否透明。这对渲染有影响，因为透明对象需要特殊处理，并在非透明对象之后渲染。
设置为true时，通过设置材质的opacity属性来控制材质透明的程度。
默认值为false。

opacity : Float
在0.0 - 1.0的范围内的浮点数，表明材质的透明度。值0.0表示完全透明，1.0表示完全不透明。
如果材质的transparent属性未设置为true，则材质将保持完全不透明，此值仅影响其颜色。 默认值为1.0。

材质混合之类的东西后面会再学习，刚入门先看看一些简单的。

*****

## 基础线条材质

LineBasicMaterial( parameters : Object )

parameters - (可选)用于定义材质外观的对象，具有一个或多个属性。材质的任何属性都可以从此处传入(包括从Material继承的任何属性)。

属性color例外，其可以作为十六进制字符串传递，默认情况下为 0xffffff（白色），内部调用Color.set(color)。

这种材质实际上我们在之前使用过，画线那一节我们使用的就是这种基础线条材质LineBasicMaterial

```js
    var scene = new THREE.Scene()

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    var camera = new THREE.PerspectiveCamera(45, window.innerHeight / window.innerHeight, 0.1, 1000)
    camera.position.z = 10

    var geometry = new THREE.PlaneGeometry(1, 20)
    var material = new THREE.LineBasicMaterial({ color: 0xffffff })

    var plane = new THREE.Mesh(geometry, material)

    scene.add(plane)

    renderer.render(scene, camera)
```

{% asset_img 1.png Markdown Preview Enhanced %}

我们尝试使用这种材质画一个平面，确实如我们所愿，因此基础线条材质并不是只能画线而已，但是画线更具优势。

下面我们使用基础线条材质画一个向右箭头，观察这种材质

```js
    var camera = new THREE.PerspectiveCamera(45, window.innerHeight / window.innerHeight, 0.1, 1000)
    camera.position.z = 20

    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(0, 1, 0))
    geometry.vertices.push(new THREE.Vector3(1, 0, 0))
    geometry.vertices.push(new THREE.Vector3(0, -1, 0))

    var material = new THREE.LineBasicMaterial()

    var line = new THREE.Line(geometry, material)

    scene.add(line)

    renderer.render(scene, camera)
```

{% asset_img 2.png Markdown Preview Enhanced %}

尝试调整不同属性：

**color : Color**
材质的颜色(Color)，默认值为白色 (0xffffff)。

改变颜色试试看，改成红色

```js
    var material = new THREE.LineBasicMaterial({ color: 0xff0000 })
```

{% asset_img 3.png Markdown Preview Enhanced %}

**linewidth : Float**
控制线宽。默认值为 1。

>由于OpenGL Core Profile与 大多数平台上WebGL渲染器的限制，无论如何设置该值，线宽始终为1。

尝试修改并没有任何变化，这个属性不生效

**linecap : String**
定义线两端的样式。可选值为 'butt', 'round' 和 'square'。默认值为 'round'。

该属性对应2D Canvas lineCap属性， 并且会被WebGL渲染器忽略。

**linejoin : String**
定义线连接节点的样式。可选值为 'round', 'bevel' 和 'miter'。默认值为 'round'。

该属性对应2D Canvas lineJoin属性， 并且会被WebGL渲染器忽略。

>如上所述，如果我们使用WebGL渲染器，那么LineBasicMaterial只有color属性是有视觉效果的。

## 虚线材质

一种用于构造虚线几何体的材质

构造函数：LineDashedMaterial( parameters : Object )

属性

* color：效果与基础线条材质一致
* linewidth：含义与基础线条材质一致
* dashSize : number 虚线的大小，是指破折号和间隙之和。默认值为 3。
* gapSize : number 间隙的大小，默认值为 1。
* scale : number 线条中虚线部分的占比。默认值为 1。

我们画一条黄色的线来观察虚线效果

```js
    camera.position.z = 100

    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(0, 10, 0))
    geometry.vertices.push(new THREE.Vector3(10, 0, 0))

    var material = new THREE.LineDashedMaterial({ color: 0xffff0f })

    var line = new THREE.Line(geometry, material)
    line.computeLineDistances(); //不可或缺的，若无，则线段不能显示为虚线
```

{% asset_img 4.png Markdown Preview Enhanced %}

线段总长度为10，根据默认值得到：3 + 1 + 3 + 1 + 2 = 10

调整属性：定义中dashSize是破折号+间隙的大小，我们设置为4，gapSize间隙大小设置为2，得到的效果

```js
    var material = new THREE.LineDashedMaterial({ color: 0xffff0f, dashSize: 4, gapSize: 2 })
```

{% asset_img 5.png Markdown Preview Enhanced %}

从效果来看是：4 + 2 + 4 = 10（dashSize + gapSize + dashSize = 线长）

因此应该这样理解dashSize和gapSize：dashSize是虚线中看得见部分大小（破折号），gapSize是虚线中看不见部分的大小（间隙）

那么scale什么用呢？

```js
    var material = new THREE.LineDashedMaterial({ color: 0xffff0f, dashSize: 4, gapSize: 2, scale: 2 })
```

{% asset_img 6.png Markdown Preview Enhanced %}

说白了就是缩放，dashSize和gapSize除以scale得到的将是每段破折号和间距真正绘制的尺寸。也就是说scale越大，虚线表现越**细密**。

公式：dashSize / scale + gapSize / scale + ... = 线长

>dashSize、gapSize、scale都是可以为0的，scale和gapSize任一为0则表现为实线，dashSize为0则虚线不可见。

下面介绍网格材质，这是threejs中最常用的一种材质。

*****

## 基础网格材质

一个以简单着色（平面或线框）方式来绘制几何体的材质。

这种材质我们之前也用过，是一种基础材质，最大的特性就是这种材质不受光照的影响。

构造函数：MeshBasicMaterial( parameters : Object )

这种材质属性大部分与纹理贴图有关，后面实例中学习这些纹理效果，现在了解两个基本属性。

color : Color 材质的颜色(Color)，默认值为白色 (0xffffff)。
wireframe : Boolean 将几何体渲染为线框。默认值为false（即渲染为平面多边形）。

渲染一个绿色平面：

```js
    var geometry = new THREE.PlaneGeometry();
    var material = new THREE.MeshBasicMaterial({ color: 0x00ff0f })
    var plane = new THREE.Mesh(geometry, material)

    scene.add(plane)
```

{% asset_img 7.png Markdown Preview Enhanced %}

将平面变为一个线框

```js
    var material = new THREE.MeshBasicMaterial({ color: 0x00ff0f, wireframe: true })
```

{% asset_img 8.png Markdown Preview Enhanced %}

>注意：分段线也会被渲染出来

## 深度网格材质

一种按深度绘制几何体的材质。深度基于相机远近平面。白色最近，黑色最远。

这种材质一定要使用贴图才会有明显效果

构造函数：MeshDepthMaterial( parameters : Object )

wireframe : boolean 将几何体渲染为线框。默认值为false（即渲染为平滑着色）。

基本使用：

```js
    camera.position.set(2, 2, 15)

    var geometry = new THREE.BoxGeometry(1, 2, 15);
    var material = new THREE.MeshDepthMaterial()
    var cube = new THREE.Mesh(geometry, material)

    scene.add(cube)

    function animate () {
        requestAnimationFrame(animate)

        cube.rotation.x += 0.01;
        cube.rotation.y += 0.01;
        cube.rotation.z += 0.01;

        renderer.render(scene, camera)

    }

    animate()
```

暂时没搞明白这种材质的用法。。。

## 其他网格材质

下面这些材质暂时搞不清楚

MeshDistanceMaterial 在内部用于使用PointLight来实现阴影映射。

MeshLambertMaterial Lambert网格材质，一种非光泽表面的材质，没有镜面高光。类似于纸的漫反射材质。

MeshMatcapMaterial 由一个材质捕捉（MatCap，或光照球（Lit Sphere））纹理所定义，其编码了材质的颜色与明暗。可以理解为自带光效果的材质

MeshNormalMaterial 法线网格材质，一种把法向量映射到RGB颜色的材质。

MeshPhongMaterial Phong网格材质，一种用于具有镜面高光的光泽表面的材质。与MeshLambertMaterial区别

MeshToonMaterial MeshPhongMaterial卡通着色的扩展。

MeshStandardMaterial 标准网格材质，一种基于物理的标准材质，在实践中，该材质提供了比MeshLambertMaterial 或MeshPhongMaterial 更精确和逼真的结果，代价是计算成本更高。对物理光照的反应是最好的。

MeshPhysicalMaterial 物理网格材质，MeshStandardMaterial的扩展，能够更好地控制反射率。

*****

## 点材质

Points（一种物体/3D对象，下一节会介绍）使用的默认材质。

构造函数：PointsMaterial( parameters : Object )

color : Color 材质的颜色(Color)，默认值为白色 (0xffffff)。
size : Number 设置点的大小。默认值为1.0。
sizeAttenuation : Boolean 指定点的大小是否因相机深度而衰减。（仅限透视摄像头。）默认为true。

```js
    var starsGeometry = new THREE.Geometry();

    for ( var i = 0; i < 10000; i ++ ) {

        var star = new THREE.Vector3();
        star.x = THREE.Math.randFloatSpread( 2000 );
        star.y = THREE.Math.randFloatSpread( 2000 );
        star.z = THREE.Math.randFloatSpread( 2000 );

        starsGeometry.vertices.push( star );

    }

    var starsMaterial = new THREE.PointsMaterial( { color: 0xff0000 } );

    var starField = new THREE.Points( starsGeometry, starsMaterial );

    scene.add( starField );


    renderer.render(scene, camera)
```

上面的代码将渲染一万个点

## 其他材质

SpriteMaterial 点精灵材质，一种使用Sprite的材质。

ShadowMaterial 阴影材质，此材质可以接收阴影，但在其他方面完全透明。

ShaderMaterial 着色器材质，使用自定义shader渲染的材质。

RawShaderMaterial 原始着色器材质

## 总结

很多材质要配合光照、纹理等才能体现出他们的特质，在学习这些内容后再回来研究理解这些材质。

目前来说：画线可以使用LineBasicMaterial，不受光照影响（物体颜色取决于材质原色）材质可以使用MeshBasicMaterial，粗糙表面可以使用MeshLambertMaterial，光滑表面可以使用MeshPhongMaterial，要求较高物理表现可以使用MeshStandardMaterial或MeshPhysicalMaterial
