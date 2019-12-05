---
title: threejs学习之路（十八）- 字体实例
date: 2019-12-04 19:30:51
categories: js
tags:
    - webgl
    - threejs
---

这是一个生成字体形状的例子

## TextGeometry

之前在学习几何形状时提到过一种字体形状，但是没有详细学习，现在我们边学习，边做一个例子

TextGeometry(text : String, parameters : Object)

text — 将要显示的文本。
parameters — 包含有下列参数的对象：

font — THREE.Font的实例。
size — Float。字体大小，默认值为100。
height — Float。挤出文本的厚度。默认值为50。
curveSegments — Integer。（表示文本的）曲线上点的数量。默认值为12。
bevelEnabled — Boolean。是否开启斜角，默认为false。
bevelThickness — Float。文本上斜角的深度，默认值为20。
bevelSize — Float。斜角与原始文本轮廓之间的延伸距离。默认值为8。
bevelSegments — Integer。斜角的分段数。默认值为3。

这个构造函数生成一个字体形状的几何体，其中有一个最重要的参数为THREE.Font

## Font

以JSON格式，创建一系列的Shape（形状）来表示一个字体。

Font( data )
data -- 表示字体的JSON数据。

这一构造函数创建一个新的Font，它是一个Shapes数组。

使用层面来说，实际上我们创建一个Font，需要表示字体的json数据，用其绘制字体形状。threejs项目examples/fonts下有很多字体json文件。

## FontLoader

怎么提供一个Font？这时候就需要之前学过的字体加载器FontLoader了

FontLoader：使用JSON格式中加载字体的一个类。返回Font, 返回值是表示字体的Shape类型的数组。 其内部使用FileLoader来加载文件。

## 一个完整的例子

```js
// 字体加载器加载字体后创建文本几何体
new THREE.FontLoader().load('./node_modules/three/examples/fonts/helvetiker_regular.typeface.json',function (font) {
    var geometry = new THREE.TextGeometry('LOVE', {
        font,
        size: 20,
        height: 4,
        curveSegments: 8,
        bevelEnable: true,
        bevelThickness: 20,
        bevelSize: 10,
        bevelSegments: 10
    })
    var mesh = new THREE.Mesh(geometry, new THREE.MeshPhongMaterial({color: 0xffffff}));
    mesh.position.x = -40;
    scene.add(mesh);
    renderer.render(scene, camera);
});
```

上面的代码创建了一个LOVE的文本几何体，但是当我们尝试创建中文字体时却不如预期所想。因为examples中提供的字体文件都不支持中文。

## 创建中文字体几何形状

为了支持中文，我们需要支持中文的字体文件。

1. 下载支持中文的ttf字体文件。
2. 到 [https://gero3.github.io/facetype.js/](https://gero3.github.io/facetype.js/) 这个网站将其转换为json文件格式。
3. 加载字体时使用该json文件即可。

中文字体文件一般都比较大，动辄好几兆，需要优化时，据说可以对其中的glyphs对象进行删减，只保留使用到的字符即可，但本人没有亲自尝试过。

## 加一点点光源特效的文本示例

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
        <script src="./node_modules/three/build/three.js"></script>
        <script src="./node_modules/three/examples/js/controls/OrbitControls.js"></script>
        <script src=""></script>

        <script type="module">
            import { default as Stats } from 'https://cdn.bootcss.com/stats.js/r17/Stats.min.js';
            var stats = new Stats();
            console.log(stats)
            document.body.appendChild(stats.domElement);
            stats.setMode(1);

            import { TWEEN } from './node_modules/three/examples/jsm/libs/tween.module.min.js';
            // Our Javascript will go here.
            var scene, renderer, camera;
            init();

            function init () {
                scene = new THREE.Scene()
                scene.background = new THREE.Color(0x111111)

                renderer = new THREE.WebGLRenderer()
                renderer.setSize(window.innerWidth, window.innerHeight)
                document.body.appendChild(renderer.domElement)

                camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
                camera.position.set(0, 0, 100);
            }

            new THREE.FontLoader().load('./node_modules/three/examples/fonts/Normal_Regular.json', function (font) {
                var geometry = new THREE.TextGeometry('你好哇', {
                    font,
                    size: 20,
                    height: 4,
                    curveSegments: 8,
                    bevelEnable: true,
                    bevelThickness: 20,
                    bevelSize: 10,
                    bevelSegments: 10
                })
                var mesh = new THREE.Mesh(geometry, new THREE.MeshPhongMaterial({color: 0xffffff}));
                mesh.position.x = -40;
                var mani1 = new TWEEN.Tween(mesh.rotation).to({x: 0, y: Math.PI / 8, z: 0}, 6000);
                var mani2 = new TWEEN.Tween(mesh.rotation).to({x: 0, y: -Math.PI / 8, z: 0}, 6000);
                mani1.chain(mani2);
                mani2.chain(mani1);
                mani1.start();
                scene.add(mesh);
                renderer.render(scene, camera);
            });

            var light = new THREE.PointLight(0xff0000, 5, 30);
            light.position.x = -20
            scene.add(light)

            var ani1 = new TWEEN.Tween(light.position).to({x: -20, y: 25, z: 0}, 4000);
            var ani2 = new TWEEN.Tween(light.position).to({x: 20, y: -5, z: 20}, 6000);
            var ani3 = new TWEEN.Tween(light.position).to({x: 30, y: 25, z: 0}, 4000);
            ani1.chain(ani2)
            ani2.chain(ani3)
            ani3.chain(ani1)
            ani1.start();

            var controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.update();

            function animate () {
                requestAnimationFrame(animate)

                controls.update()
                // stats.begin();
                // stats.end();

                TWEEN.update()

                stats.update();

                renderer.render(scene, camera)
            }

            animate()

        </script>
    </body>
</html>
```

{% asset_img 1.png Markdown Preview Enhanced %}

## 总结

学会这个就可以创建一些简单的文本几何形状了
