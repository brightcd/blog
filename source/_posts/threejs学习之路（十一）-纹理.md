---
title: threejs学习之路（十一）-- 纹理
date: 2019-11-25 16:37:42
categories: js
tags:
    - webgl
    - threejs
---

纹理可以看做一个贴图

## Texture

创建一个纹理贴图，将其应用到一个表面，或者作为反射/折射贴图。

Texture( image, mapping, wrapS, wrapT, magFilter, minFilter, format, type, anisotropy, encoding )

image : Image
一个图片对象，通常由TextureLoader.load方法创建。 该对象可以是被three.js所支持的任意图片（例如PNG、JPG、GIF、DDS）或视频（例如MP4、OGG/OGV）格式。

要使用视频来作为纹理贴图，你需要有一个正在播放的HTML5 Video元素来作为你纹理贴图的源图像， 并在视频播放时不断地更新这个纹理贴图。——VideoTexture 类会对此自动进行处理。

mapping : number
图像将如何应用到物体（对象）上。默认值是THREE.UVMapping对象类型， 即UV坐标将被用于纹理映射。

wrapS : number
这个值定义了纹理贴图在水平方向上将如何包裹，在UV映射中对应于U。
默认值是THREE.ClampToEdgeWrapping，即纹理边缘将被推到外部边缘的纹素。 其它的两个选项分别是THREE.RepeatWrapping和THREE.MirroredRepeatWrapping。

wrapT : number
这个值定义了纹理贴图在垂直方向上将如何包裹，在UV映射中对应于V。
可以使用与 .wrapS : number相同的选项。

请注意：纹理中图像的平铺，仅有当图像大小（以像素为单位）为2的幂（2、4、8、16、32、64、128、256、512、1024、2048、……）时才起作用。 宽度、高度无需相等，但每个维度的长度必须都是2的幂。 这是WebGL中的限制，不是由three.js所限制的。

magFilter : number
当一个纹素覆盖大于一个像素时，贴图将如何采样。默认值为THREE.LinearFilter， 它将获取四个最接近的纹素，并在他们之间进行双线性插值。 另一个选项是THREE.NearestFilter，它将使用最接近的纹素的值。
请参阅texture constants页面来了解详细信息。

minFilter : number
当一个纹素覆盖小于一个像素时，贴图将如何采样。默认值为THREE.LinearMipmapLinearFilter， 它将使用mipmapping以及三次线性滤镜。

anisotropy : number
沿着轴，通过具有最高纹素密度的像素的样本数。 默认情况下，这个值为1。设置一个较高的值将会产生比基本的mipmap更清晰的效果，代价是需要使用更多纹理样本。 使用renderer.getMaxAnisotropy() 来查询GPU中各向异性的最大有效值；这个值通常是2的幂。

format : number
默认值为THREE.RGBAFormat， 但TextureLoader将会在载入JPG图片时自动将这个值设置为THREE.RGBFormat。

type : number
这个值必须与.format相对应。默认值为THREE.UnsignedByteType， 它将会被用于绝大多数纹理格式。

## 纹理类型

### Canvas纹理（CanvasTexture）

从Canvas元素中创建纹理贴图。

CanvasTexture( canvas : HTMLElement, mapping : Constant, wrapS : Constant, wrapT : Constant, magFilter : Constant, minFilter : Constant, format : Constant, type : Constant, anisotropy : Number )

canvas -- 将会被用于加载纹理贴图的Canvas元素。

### 压缩的纹理（CompressedTexture）

基于被压缩的数据，创建一个纹理贴图，例如从一个DDS文件中。

它和CompressedTextureLoader一起使用。

CompressedTexture( mipmaps : Array, width : Number, height : Number, format : Constant, type : Constant, mapping : Constant, wrapS : Constant, wrapT : Constant, magFilter : Constant, minFilter : Constant, anisotropy : Number )

mipmaps -- mipmaps数组中需要包含具有数据、宽、高的对象。mipmaps应当具有正确的格式与类型。
width -- 最大的mipmap的宽。
height -- 最大的mipmap的高。

### 立方纹理（CubeTexture）

创建一个由6张图片所组成的纹理对象。

CubeTexture( images, mapping, wrapS, wrapT, magFilter, minFilter, format, type, anisotropy )
CubeTexture（立方贴图）的功能以及用法几乎和Texture是相同的。区别在于，CubeTexture中的图像是6个单独的图像所组成的数组， 纹理映射选项为THREE.CubeReflectionMapping（默认值）或THREE.CubeRefractionMapping。

### 深度纹理（DepthTexture）

创建一个作为深度纹理贴图来使用的纹理。需要支持WEBGL_depth_texture扩展。

DepthTexture( width : Number, height : Number, type : Constant, wrapS : Constant, wrapT : Constant, magFilter : Constant, minFilter : Constant, anisotropy : Number, format : Constant )
width -- 纹理的宽度。
height -- 纹理的高度。

### 视频纹理（VideoTexture）

创建一个使用视频来作为贴图的纹理对象。

它和其基类Texture几乎是相同的，除了它总是将needsUpdate设置为true，以便使得贴图能够在视频播放时进行更新。自动创建mipmaps也会被禁用。

VideoTexture( video : Video, mapping : Constant, wrapS : Constant, wrapT : Constant, magFilter : Constant, minFilter : Constant, format : Constant, type : Constant, anisotropy : Number )

video -- 将被作为纹理贴图来使用的Video元素。

## 示例

一个基本的使用纹理场景

```js
    var texture = new THREE.TextureLoader().load('3.png', function () {
        // 纹理加载后一定要重新渲染一次
        renderer.render(scene, camera)
    })

    var scene = new THREE.Scene();

    var renderer = new THREE.WebGLRenderer()
    renderer.setSize(window.innerWidth, window.innerHeight)
    document.body.appendChild(renderer.domElement)

    var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
    camera.position.set(0, 0, 5)

    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var material = new THREE.MeshBasicMaterial({map: texture})

    var cube = new THREE.Mesh(geometry, material)

    scene.add(cube)

    function animate () {
        requestAnimationFrame(animate)

        cube.rotation.x += 0.01
        cube.rotation.y += 0.01
        cube.rotation.z += 0.01

        renderer.render(scene, camera)
    }
    animate()
```

{% asset_img 1.png Markdown Preview Enhanced %}

稍微改变下纹理的包裹方式（平铺效果）

```js
    texture.wrapS = THREE.RepeatWrapping
    texture.wrapT = THREE.RepeatWrapping
    // 平铺重复次数不设置默认为1
    texture.repeat.set( 8, 8 );
```

{% asset_img 2.png Markdown Preview Enhanced %}

## 总结

纹理简单来说就是一张贴图，但是贴图怎么使用，根据颜色、透明度、光等做不同的贴图的效果是很讲究的。
