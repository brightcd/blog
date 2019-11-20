---
title: threejs学习之路（四）-- 透视相机
date: 2019-11-20 10:23:19
categories: js
tags: 
    - webgl
    - threejs
---

与正交相机不同，透视相机采用透视投影模式，用来模拟人眼看到的景象，它是3D场景渲染中使用最普遍的投影模式。简单来说，透视投影就像我们现实中看到的场景，近大远小，就像美术老师画的那样。

## 透视相机

{% asset_img perspective.png Markdown Preview Enhanced %}

如图所示，绿色部分为视锥体（view frustum），同样，视锥体内的场景才能被摄像机捕获

## 透视相机构造器

PerspectiveCamera( fov : Number, aspect : Number, near : Number, far : Number )

* fov — 摄像机视锥体垂直视野角度
* aspect — 摄像机视锥体长宽比
* near — 摄像机视锥体近端面
* far — 摄像机视锥体远端面

```js
    var scene = new THREE.Scene()

    var camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 0.1, 1000)
    camera.position.z = 5

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true })
    var cube = new THREE.Mesh(geometry, material)

    scene.add(cube)

    renderer.render(scene, camera)
```

{% asset_img 1.png Markdown Preview Enhanced %}

当我们改变垂直视野的角度，增大fov，此时视景区（视野范围/可视区域）增大了，物体相对渲染视图画面变小了

```js
    var camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000)
```

{% asset_img 2.png Markdown Preview Enhanced %}

当增大视锥体长宽比，视景区变宽了，同样道理，物体相对变窄了

```js
     var camera = new THREE.PerspectiveCamera(45, window.innerWidth * 2/window.innerHeight, 0.1, 1000)
```

{% asset_img 3.png Markdown Preview Enhanced %}

near和far确定的面决定了场景可视区大小，他们的值同正交相机意义一样，表示的是距离视点的数值距离

(?此处有疑问，之后解决...)

如下，可视区把立方体切掉一块(near = 8)

```js
    var camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 8, 1000)
    camera.position.set(5, 5, 5)
    camera.lookAt(new THREE.Vector3(0, 0, 0))
```

{% asset_img 4.png Markdown Preview Enhanced %}

尝试调整相机的位置，如预期一样，物体整体确实会呈现近大远小的效果

```js
    camera.position.z = 15
```

{% asset_img 5.png Markdown Preview Enhanced %}

## 总结

以上就是透视投影相机的效果，总的来说，透视现象存在于我们现实的三维世界中，因此我们人眼看到的景象就是透视相机拍摄场景的效果。

扩展：实时改变相机的拍摄位置，实现动画效果

```js
    var camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 0.1, 1000)
    camera.position.set(5, 5, 5)
    camera.lookAt(new THREE.Vector3(0, 0, 0))

    // something others ......

    function animate () {
        requestAnimationFrame(animate)

        camera.position.x += 0.01
        camera.position.y += 0.01
        camera.position.z += 0.01

        renderer.render(scene, camera)
    }

    animate()
```
