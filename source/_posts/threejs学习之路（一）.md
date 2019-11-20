---
title: threejs学习之路（一）
date: 2019-11-19 11:30:13
categories: js
tags: 
    - webgl
    - threejs
---

>最近开始学习webGL，从threejs开始吧，后续是个人的一些学习过程记录，也许会比较凌乱，如有错漏，欢迎指正~

今天是一个开始，从threejs入手，来一个Get Start吧

[ThreeJS官网](https://threejs.org/)(有[中文](https://threejs.org/docs/index.html#manual/zh/introduction/Creating-a-scene)哈)

我们的目标是绘制一个旋转的立方体

## 准备

### 本地搭建简单http服务

由于three.js中的纹理等资源需要外部加载，为避免浏览器同源策略的影响，在本地搭建一个方便的http服务，方便以后开发

>对nodejs和npm还不熟悉的同学请自行百度

```bash
    # 安装
    $ npm install -g http-server
    # 运行http-server
    $ http-server . -p 8000
    # http-server命令后指定根目录，-p指定端口
```

http-server是npm提供的一个简单的本地http服务器，运行之后可以在浏览器中输入http://127.0.0.1:8000 访问本地静态资源

### 引入three.js

为学习方便，我们直接在html中通过script标签引入three.js，随便使用一个[cdn](https://cdnjs.cloudflare.com/ajax/libs/three.js/109/three.js)，网上有很多，随便搜一下就有，bootstrap也提供了；当然，使用three.js的方式有很多，包括方便的npm等，可以在[官网上找到具体详细的使用方法](https://threejs.org/docs/index.html#manual/zh/introduction/Import-via-modules)，根据自己的场景选择合适的方式就行。

html代码：

```html
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="utf-8">
            <title>My first three.js app</title>
            <style>
                body { margin: 0; }
                canvas { width: 100%; height: 100% }
            </style>
        </head>
        <body>
            <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/109/three.js"></script>
            <script>
                // Our Javascript will go here.

            </script>
        </body>
    </html>
```

## 开始

### 一些基本概念

引入three.js之后，会有一个全局变量THREE，所有的api都挂载在它下面

一个threejs应用必不可少的元素

* 场景Scene：可以想象成容纳3D对象的现实世界
* 相机Camera：跟现实中的相机意义一样，决定视区，也可以近似理解为人的眼睛
* 渲染器Renderer：渲染器的作用就是把相机看到的场景渲染到画布上（目前webGLRenderer的支持度已经比较好了）

### 简单的坐标世界

我们的场景中是一个三维世界，场景的中央为原点（0，0，0），X轴方向从左到右，Y轴方向从下到上，Z轴方向从远到近（不知道这样大家能不能理解）

{% asset_img location.png Markdown Preview Enhanced %}

### 编写绘制相关代码

>以下代码写入上面html的script标签中

创建一个场景（我们的世界）

```js
    var scene = new THREE.Scene()
```

创建一个透视投影相机（摆好姿势准备拍摄）

```js
    var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
    camera.position.z = 5
    camera.lookAt(0, 0, 0)
```

创建一个WebGL渲染器（画布/胶片）

```js
    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)
```

创建一个立方体（可以想象为创建一张桌子）

```js
    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var material = new THREE.MeshBasicMaterial({ color: 0xff0000 })
    var cube = new THREE.Mesh(geometry, material)
```

将立方体添加到场景中（桌子放到我们的世界里）

```js
    scene.add(cube)
```

用渲染器将相机看到的场景渲染到画布（把眼前当时当帧的场景绘制出来/打到胶片上）

```js
    renderer.render(scene, camera)
```

至此，一个立方体绘制到显卡上的操作就完成了，其中的细节后续会详细学习介绍。

下面让立方体旋转起来

修改最后一步渲染过程：

```js
    function animate () {
        requestAnimationFrame(animate)

        cube.rotation.x += 0.01
        cube.rotation.y += 0.01

        renderer.render(scene, camera)
    }

    animate()
```

利用requestAnimationFrame API逐帧渲染，可以实现流畅的动画，至于为什么不用setInterval，可以自己去思考一下区别~

## 总结

大部分的绘制过程都是上述的流程，只不过过程更复杂而已，从最简单的开始，这个红方块是一个好的开始
