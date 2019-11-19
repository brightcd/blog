---
title: threejs学习之路（二）-- 画线
date: 2019-11-19 16:37:14
categories: js
tags: 
    - webgl
    - threejs
---

上节我们绘制了一个旋转的立方体，再接再厉，我们来练习画线

## 例行流程

开启本地http-server，引入threejs

```html
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="utf-8">
            <title>My second three.js app</title>
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
    </html>`
```

创建一个场景 => 一个相机 => 一个渲染器

```js
    // 创建一个场景
    var scene = new THREE.Scene()

    // 透视投影相机
    var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
    camera.position.z = 100
    camera.lookAt(0, 0, 0)

    // 创建渲染器，流行的WebGL渲染器
    var renderer = new THREE.WebGLRenderer()
    // 设置canvas画布的大小
    renderer.setSize(window.innerWidth, window.innerHeight)
    // canvas元素加入页面
    document.body.appendChild(renderer.domElement)
```

## 绘制内容

绘制两条线

```js
    // 几何形状
    var geometry = new THREE.Geometry()
    geometry.vertices.push(new THREE.Vector3(-10, 0, 0))
    geometry.vertices.push(new THREE.Vector3(0, 10, 0))
    geometry.vertices.push(new THREE.Vector3(10, 0, 0))
    // 覆盖材质
    var material = new THREE.LineBasicMaterial({ color: 0xff00ff })

    var line = new THREE.Line(geometry, material)
```

>从上面的代码中我们可以简单看出，一个3D实物对象是由他的几何形体和覆盖表面的材质构成的，这与三维现实中的物体概念一致；同时绘制直线的形状是由顶点坐标确定的

将线加入场景中并渲染到画布

```js
    // 加入场景中
    scene.add(line)

    // 渲染相机看到的
    renderer.render(scene, camera);
```

可以看到屏幕上绘制出一个紫色的向上箭头

## 总结

绘制一个3D对象，需要确认其几何形体和渲染材质

扩展：让箭头的颜色不停变换

```js
    let s = false
    function animate () {
        requestAnimationFrame(animate)

        if (s) {
            line.material.color = new THREE.Color(0xffff00)
        } else {
            line.material.color = new THREE.Color(0xff00ff)
        }

        s = !s
        renderer.render(scene, camera);
    }

    animate()
```
