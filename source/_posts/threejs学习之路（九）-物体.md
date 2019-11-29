---
title: threejs学习之路（九）-- 物体
date: 2019-11-25 10:11:18
categories: js
tags:
    - webgl
    - threejs
---

几何形状和材质，需要结合起来形成相应的物体才能加入3D世界。

## 三维物体

Object3D是Three.js中大部分对象的基类，提供了一系列的属性和方法来对三维空间中的物体进行操纵。

>请注意，可以通过.add( object )方法来将对象进行组合，该方法将对象添加为子对象，但为此最好使用Group（来作为父对象）。

一些简单的属性和方法:

children : Object3D
含有对象的子级的数组。

parent : Object3D
在scene graph（场景图）中，一个对象的父级对象。

position : Vector3
表示对象局部位置的Vector3

renderOrder : Number
这个值将使得scene graph（场景图）中默认的的渲染顺序被覆盖， 即使不透明对象和透明对象保持独立顺序。 渲染顺序是由低到高来排序的，默认值为0。

rotation : Euler
物体的局部旋转，以弧度来表示。

scale : Vector3
物体的局部缩放。默认值是Vector3( 1, 1, 1 )。

visible : Boolean
可见性。这个值为true时，物体将被渲染。默认值为true。

add ( object : Object3D, ... ) : null
添加对象到这个对象的子级，可以添加任意数量的对象。 当前传入的对象中的父级将在这里被移除，因为一个对象仅能有一个父级。

rotateX|Y|Z ( rad : Float ) : this
rad - 将要旋转的角度（以弧度来表示）。

translateX|Y|Z ( distance : Float ) : this
沿着X|Y|Z轴将平移distance个单位。

## 点

一个用于显示点的类，由WebGLRenderer渲染的点使用 gl.POINTS

构造函数：Points( geometry : Geometry, material : Material )

geometry —— （可选）是一个Geometry或者BufferGeometry的实例，默认值是一个新的BufferGeometry。
material —— （可选） 是一个对象，默认值是一个具有随机颜色的新的PointsMaterial。

结合上一节中的点材质绘制一个点

```js
    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(0, 0, 0))
    var material = new THREE.PointsMaterial({ color: 0xf37b1d, size: 10, sizeAttenuation: false })
    var point = new THREE.Points(geometry, material)

    scene.add(point)
```

{% asset_img 1.png Markdown Preview Enhanced %}

创建一个不随相机深度衰减的点，可以看到点至少有一个顶点，其次它是一个正方形，就像一个二维图像一样，无论从什么角度观察点，都是一个正方形。

## 线

### 线段

LineSegments( geometry : Geometry, material : Material )
geometry —— 表示每条线段的两个顶点。
material —— 线的材质，默认值是LineBasicMaterial。

如果没有指定材质，一个随机颜色的线的材质将会被创建，并应用到该物体上。

创建一条线段

```js
    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(-5, 0, 0))
    geometry.vertices.push(new THREE.Vector3(5, 0, 0))
    var material = new THREE.LineBasicMaterial({ color: 0xf37b1d })
    var line = new THREE.LineSegments(geometry, material)

    scene.add(line)
```

{% asset_img 2.png Markdown Preview Enhanced %}

### 连续线

Line( geometry : Geometry, material : Material )

一条连续的线，它几乎和LineSegments是一样的，唯一的区别是它在渲染时使用的是gl.LINE_STRIP， 而不是gl.LINES。

当然，线段只能由两个顶点画一条直线，Line可以有多个顶点，依次按顺序画出多条线段，首尾顶点不会自动相连封闭

```js
    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(-5, 0, 0))
    geometry.vertices.push(new THREE.Vector3(5, 0, 0))
    geometry.vertices.push(new THREE.Vector3(5, 5, 0))
    var material = new THREE.LineBasicMaterial({ color: 0xf37b1d })
    var line = new THREE.Line(geometry, material)

    scene.add(line)
```

{% asset_img 3.png Markdown Preview Enhanced %}

### 环线

LineLoop( geometry : Geometry, material : Material )

一条首尾相接的连续的线

它几乎和Line是相同的，唯一的区别是它在渲染时使用的是gl.LINE_LOOP， 而不是gl.LINE_STRIP， 它绘制一条直线到下一个顶点，并将最后一个顶点连回第一个顶点。

```js
    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(-5, 0, 0))
    geometry.vertices.push(new THREE.Vector3(5, 0, 0))
    geometry.vertices.push(new THREE.Vector3(5, 5, 0))
    var line = new THREE.LineLoop(geometry)

    scene.add(line)
```

{% asset_img 4.png Markdown Preview Enhanced %}

## 网格

表示基于以三角形为polygon mesh（多边形网格）的物体的类。

Mesh( geometry : Geometry, material : Material )
geometry —— （可选）Geometry或者BufferGeometry的实例，默认值是一个新的BufferGeometry。
material —— （可选）一个Material，或是一个包含有Material的数组，默认是一个新的MeshBasicMaterial。

Mesh是最基础的物体，这种网格物体可以模拟大部分3D对象，我们之前的例子中也用到很多次，这里不写示例了。

InstancedMesh( geometry : BufferGeometry, material : Material, count : Integer )
如果要绘制大量几何形状和材质都相同的物体，可以使用InstanceMesh代替Mesh可以减少渲染调用，提升性能。

### 多细节层次

多细节层次（LOD，Levels of Detail）
多细节层次 —— 在显示网格时，根据摄像机距离物体的距离，来使用更多或者更少的几何体来对其进行显示。

每一个级别都和一个几何体相关联，且在渲染时，可以根据给定的距离，来在这些级别对应的几何体之间进行切换。 通常情况下，你会创建多个几何体，比如说三个，一个距离很远（低细节），一个距离适中（中等细节），还有一个距离非常近（高质量）。

构造函数：LOD()

levels : array 一个包含有level objects（各层次物体）的数组。
每一个层级都是一个对象，具有以下两个属性： object —— 在这个层次中将要显示的Object3D。
distance —— 将显示这一细节层次的距离。

addLevel ( object : Object3D, distance : Float ) : this
object —— 在这个层次中将要显示的Object3D。
distance —— 将显示这一细节层次的距离。

```js
    var lod = new THREE.LOD();

    for( var i = 0; i < 3; i++ ) {

        var geometry = new THREE.IcosahedronBufferGeometry( 10, 3 - i )
        var material = new THREE.MeshBasicMaterial({wireframe: true})
        var mesh = new THREE.Mesh( geometry, material )

        lod.addLevel( mesh, i * 75 );

    }

    scene.add( lod );
```

实际上lod绘制了多个网格物体，通过与相机的距离distance来决定要显示哪个物体，不同的物体一般在不同的细节层次上，每个距离对应一个网格物体，也就是说根据不同的距离展示不同细节层次的物体。

```js
    camera.position.set(0, 0, 200)
```

{% asset_img 5.png Markdown Preview Enhanced %}

```js
    camera.position.set(0, 0, 100)
```

{% asset_img 6.png Markdown Preview Enhanced %}

```js
    camera.position.set(0, 0, 70)
```

{% asset_img 7.png Markdown Preview Enhanced %}

## 骨

### 骨骼

骨骼是Skeleton（骨架）的一部分。骨架是由SkinnedMesh（蒙皮网格）依次来使用的。 骨骼几乎和空白Object3D相同。

Bone() 创建一个新的Bone.

### 骨架

使用一个bones数组来创建一个可以由SkinnedMesh使用的骨架。

Skeleton( bones : Array, boneInverses : Array )
bones —— 包含有一组bone的数组，默认值是一个空数组。
boneInverses —— （可选） 包含Matrix4的数组。

```js
    var bones = []

    var shoulder = new THREE.Bone()
    var elbow = new THREE.Bone()
    var hand = new THREE.Bone()

    shoulder.add(elbow)
    elbow.add(hand)

    bones.push(shoulder, elbow, hand)

    shoulder.position.y = -5
    elbow.position.y = 0
    hand.position.y = 5

    var armSkeleton = new THREE.Skeleton(bones)
```

### 蒙皮网格

具有Skeleton（骨架）和bones（骨骼）的网格，可用于给几何体上的顶点添加动画。 其材质必须支持蒙皮，并且已经启用了蒙皮。

>可以理解为骨骼构成骨架，向骨架贴皮肤（蒙皮）的过程

SkinnedMesh( geometry : BufferGeometry, material : Material )
geometry —— 一个BufferGeometry实例。
material —— （可选）一个Material实例，默认值是一个新的MeshBasicMaterial。

```js
    var geometry = new THREE.CylinderBufferGeometry( 5, 5, 10, 5, 3, false);

    // 材质支持并启用蒙皮
    var material = new THREE.MeshBasicMaterial({skinning: true, wireframe: true})
    var mesh = new THREE.SkinnedMesh(geometry, material)

    // 必须将一个骨架绑定到蒙皮网格上
    mesh.bind(armSkeleton)

    scene.add(mesh)
```

{% asset_img 8.png Markdown Preview Enhanced %}

如上我们创建了一个蒙皮网格，并将之前的骨架绑定到上面，实现了普通Mesh的效果。

但蒙皮网格的用处不在这里，想象一下我们将几何体做成蒙皮网格之后，由于蒙皮上绑定了骨架，因此我们骨架变动便会带动物体变动，类似人类的手，骨架运动带动了手的运动，因此蒙皮网格大多用于一些骨骼动画。

现在通过变动骨架使手臂抡起来

```js
    mesh.add(armSkeleton.bones[0])

    function animate () {
        requestAnimationFrame(animate)

        armSkeleton.bones[0].rotation.z += 0.01

        renderer.render(scene, camera)
    }

    animate()
```

{% asset_img 9.png Markdown Preview Enhanced %}

ok，接下来我们不想抡手臂了，我们让手肘动起来，在这之前，先了解几何体基类Geometry的两个属性：

**skinWeights : Array**
当在处理一个 SkinnedMesh 时，每个顶点最多可以有 4 个相关的 bones 来影响它。 skinWeights 属性是一个权重队列，顺序同几何体中的顶点保持一致。因而，队列中的第一个 skinWeight 就对应几何体中的第一个顶点。由于每个顶点可以被 4 个 bones 营销，因而每个顶点的 skinWeights 就采用一个 Vector4 表示。

skinWeight 矢量中每个元素的取值范围应该在 0 到 1 之间。例如，当设置为 0，骨骼对该顶点的位置没有影响。当设置为 0.5, 则对顶点的影响为 50%。 当设置为 100% 则对顶点的影响是 100%。如果矢量中只有一个骨骼与顶点相关联，则你只需要关注矢量中的第一个元素， 剩余的元素可以忽略，他们的值可以都设置为 0。

**skinIndices : Array**
就如同 skinWeights 属性一样。skinIndices 的值也是与几何体的顶点相对应。每个顶点可以最多有 4 个骨骼与之相关联。 因而第一个 skinIndex 就与几何体的第一个顶点相关联，skinIndex 的值就指明了影响该顶点的骨骼是哪个。例如，第一个顶点的值是 ( 10.05, 30.10, 12.12 )，第一个 skinIndex 的值是( 10, 2, 0, 0 )，第一个 skinWeight 的值是 ( 0.8, 0.2, 0, 0 )。上述值表明第一个顶点受到skeleton.bones[10]骨骼的影响有 80%， 受到 skeleton.bones[2] 的影响是 20%，由于另外两个 skinWeight 的值是 0，因而他们对顶点没有任何影响。

简单来说就是每个顶点可以由最多4个骨骼影响，skinIndices 数组长度就是几何体顶点数（取决于几何体本身和分段数，这里能看出分段数有用了，哈哈），其中每个值Vector4（四个一组）表示这个顶点由哪四个骨骼影响，而skinWeights表示每个骨骼的影响权重

```js
    var geometry = new THREE.CylinderBufferGeometry( 5, 5, 10, 5, 15, false);

    // 拿到所有顶点（attributes里存储了geometry的顶点、面、颜色等信息）
    var position = geometry.attributes.position

    // 骨骼数组
    var skinIndices = []
    // 权重数组
    var skinWeights = []

    var vertex = new THREE.Vector4()

    for (let i = 0; i < position.count; i++) {
        vertex.fromBufferAttribute(position, i)

        var y = vertex.y + 5;

        // 计算骨骼对每个顶点的影响
        var skinIndex = Math.floor( y / 15 );
        var skinWeight = ( y % 15 ) / 15;

        skinIndices.push( skinIndex, skinIndex + 1, 0, 0 );
        skinWeights.push( 1 - skinWeight, skinWeight, 0, 0 );

    }

    geometry.setAttribute( 'skinIndex', new THREE.Uint16BufferAttribute( skinIndices, 4 ) );
    geometry.setAttribute( 'skinWeight', new THREE.Float32BufferAttribute( skinWeights, 4 ) );

    // 材质支持并启用蒙皮
    var material = new THREE.MeshBasicMaterial({skinning: true, wireframe: true})
    var mesh = new THREE.SkinnedMesh(geometry, material)

    mesh.add(armSkeleton.bones[0])

    // 必须将一个骨骼绑定到蒙皮网格上
    mesh.bind(armSkeleton)

    scene.add(mesh)

    renderer.render(scene, camera)

    function animate () {
        requestAnimationFrame(animate)

        armSkeleton.bones[1].rotation.z += 0.01
        armSkeleton.bones[1].rotation.x += 0.01
        armSkeleton.bones[1].rotation.y += 0.01

        renderer.render(scene, camera)
    }

    animate()
```

{% asset_img 10.png Markdown Preview Enhanced %}

骨骼动画水很深，后面有时间再深入研究。。。

## 组

它几乎和Object3D是相同的，其目的是使得组中对象在语法上的结构更加清晰。

构造函数：Group()

```js
    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var material = new THREE.MeshBasicMaterial()

    var cube1 = new THREE.Mesh(geometry, material)
    cube1.position.set(3, 0, 0)

    var cube2 = new THREE.Mesh(geometry, material)

    var group = new THREE.Group()

    group.add(cube1)
    group.add(cube2)

    scene.add(group)

    function animate () {
        requestAnimationFrame(animate)

        group.rotation.z += 0.01
        group.rotation.x += 0.01
        group.rotation.y += 0.01

        renderer.render(scene, camera)
    }

    animate()
```

当然，整个group既然是一个Object3D，那么整个组也可以一起动起来

## 精灵

精灵（Sprite）是一个总是面朝着摄像机的平面，通常含有使用一个半透明的纹理。

精灵不会投射任何阴影，即使设置了castShadow = true也将不会有任何效果。

Sprite( material : Material )
material - （可选值）是SpriteMaterial的一个实例。 默认值是一个白色的SpriteMaterial。

```js
    camera.position.set(5, 10, 10)
    // 点精灵材质
    var material = new THREE.SpriteMaterial({color: 0x55ff00})
    var sprite = new THREE.Sprite(material);

    scene.add(sprite)

    renderer.render(scene, camera)
```

无论摄像机怎么变，精灵总是面向摄像机的一个正方形。(这里用到了我们之前学习的点精灵材质)

## 总结

学习到这里，我们已经可以在场景中添加各种3D物体了，并且可以通过调整相机展现它们的不同状态。
