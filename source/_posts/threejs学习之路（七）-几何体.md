---
title: threejs学习之路（七）-- 几何体
date: 2019-11-20 17:35:02
categories: js
tags:
    - webgl
    - threejs
---

学过素描的同学都知道，世间万物都可以看做是由基本几何形状构成，所以我们学习绘画都是先画简单几何体，WebGL世界也是一样的，准确来说它是3D场景在2D世界的投影，跟我们拍照一个原理。

后面的学习中我们先来看看threejs 3D世界中的一些基本几何体

## 几何基类

threejs中几何体基类是Geometry，试想一下中学学的构成几何体无非就是点线面，所以Geometry中实际上就是操作这些点线面

> BufferGeometry 是面片、线或点几何体的有效表述。包括顶点位置，面片索引、法相量、颜色值、UV 坐标和自定义缓存属性值。使用 BufferGeometry 可以有效减少向 GPU 传输上述数据所需的开销。但是由于 Geometry 比操作 BufferGeometry 简单得多（BufferGeometry需要bufferArray支持），因此，我们先讨论Geometry类型

构造函数：Geometry() 没有任何参数

### 基本属性

* vertices : Array 顶点的队列，保存了模型中每个顶点的位置信息。
* colors : Array 顶点 colors 队列，与顶点数量和顺序保持一致。
* faces : Array 描述每个顶点之间如何组成模型面的面队列。同时该队列保存面和顶点的法向量和颜色信息。

要了解上述属性，我们来插播几个知识点：

1. 顶点：世间万物由基本的点构成，在三维世界中描述一个点的位置只需要三个坐标值，因此threejs通过三维向量（Vector3( x : Float, y : Float, z : Float )）来表示一个顶点
2. 颜色：threejs中构造一个颜色对象，Color( r : Color_Hex_or_String, g : Float, b : Float )
r - (可选参数) 如果参数g和b被定义，则r表示颜色中的红色分量。 如果未被定义，r可以是一个十六进制 hexadecimal triplet 颜色值或CSS样式的字符串或一个Color实例。
g - (可选参数) 如果被定义，表示颜色中的绿色分量。
b - (可选参数) 如果被定义，表示颜色中的蓝色分量。
3. 三角面：Face3( a : Integer, b : Integer, c : Integer, normal : Vector3, color : Color, materialIndex : Integer )，在threejs中*所有的几何形状都是由三角面构成的*

在自定义几何体中我们将详细介绍这些属性

### 基本方法

lookAt ( vector : Vector3 ) : Geometry
该方法将几何体进行旋转，是的几何体朝向参数指定的世界坐标。

rotateX ( radians : Float ) : Geometry
将几何体绕 X 轴旋转参数指定度数。

rotateY ( radians : Float ) : Geometry
将几何体绕 Y 轴旋转参数指定度数。

rotateZ ( radians : Float ) : Geometry
将几何体绕 Z 轴旋转参数指定度数。

scale ( x : Float, y : Float, z : Float ) : Geometry
缩放几何体大小。

translate ( x : Float, y : Float, z : Float ) : Geometry
移动当前几何体。

上述方法都有该操作通常在一次处理中完成，但不会在渲染过程中处理。也就是说可以在创建几何体时通过调用这些方法改变几何体的状态，但是渲染每帧的过程中通过上述方法调用是不会有预期效果的

*****

## 立方几何体

实际上就是现实生活中的正方体长方体，且只能创建这两种

BoxGeometry(width : Float, height : Float, depth : Float, widthSegments : Integer, heightSegments : Integer, depthSegments : Integer)

width — X轴上面的宽度，默认值为1。
height — Y轴上面的高度，默认值为1。
depth — Z轴上面的深度，默认值为1。
widthSegments — （可选）宽度的分段数，默认值是1。
heightSegments — （可选）高度的分段数，默认值是1。
depthSegments — （可选）深度的分段数，默认值是1。

>暂时还不知道分段数有什么用...

```js
    var geometry = new THREE.BoxGeometry(1, 1, 1)
    var material = new THREE.MeshBasicMaterial({ color: 0xffffff})
    var cube = new THREE.Mesh(geometry, material)

    var geometry2 = new THREE.BoxGeometry(3, 1, 0.1)
    var cube2 = new THREE.Mesh(geometry2, material)
    cube2.position.x = 3;
    cube2.position.y = 2;

    scene.add(cube, cube2)

    function animate () {
        requestAnimationFrame(animate)

        cube.rotation.x += 0.01
        cube.rotation.y += 0.01

        renderer.render(scene, camera)
    }
    animate()
```

前三个参数分别表示在各轴上的宽度，透视相机看来就是标准的立方几何体效果

{% asset_img 1.png Markdown Preview Enhanced %}

*****

## 圆形

是根据一定半径由多个三角面构成的平面

定义上是绘制圆形，但是从下面的例子我们可以看出，实际上这个方法绘制的是一个**多边形**。

CircleGeometry(radius : Float, segments : Integer, thetaStart : Float, thetaLength : Float)

radius — 圆形的半径，默认值为1
segments — 分段（三角面）的数量，最小值为3，默认值为8。
thetaStart — 第一个分段的起始角度，默认为0。（three o'clock position）
thetaLength — 圆形扇区的中心角，通常被称为“θ”（西塔）。默认值是2*Pi，这使其成为一个完整的圆。

绘制一个默认的“圆形”：

```js
    var geometry = new THREE.CircleGeometry()
    var material = new THREE.MeshBasicMaterial({ color: 0xf37b1d })
    var circle = new THREE.Mesh(geometry, material)

    scene.add(circle)

    renderer.render(scene, camera)
```

{% asset_img 2.png Markdown Preview Enhanced %}

从实际效果来看绘制出的是一个八边线，参数segments分段数默认为8，实际上由8个三角面组成了这个图形。

把分段数改为3试试：

```js
    var geometry = new THREE.CircleGeometry(1, 3)
```

{% asset_img 3.png Markdown Preview Enhanced %}

如上图，我们绘制了一个三角形，当然这个三角形也是由三个三角面构成的

综上两个例子可以看出这个方法用于绘制一个等边多边形，半径决定了多边形大小，分段数决定了绘制出来的是几边形。

绘制一个正方形：

```js
    var geometry = new THREE.CircleGeometry(1, 4)
    geometry.rotateZ(2 * Math.PI / 8);
```

{% asset_img 4.png Markdown Preview Enhanced %}

现在要绘制一个圆形，打开思路：圆形其实就是n趋于无穷的n边型，之前说threejs中所有几何体都是由三角面构成的，那么圆形也不例外，理论上我们增大segments为无穷大，将能由无数个三角面构成一个圆形。

思路是正确的✅，但是实际上我们只需要根据radius半径的大小，设置足够多的segments分段数，使图形达到视觉上的圆形就可以了。

```js
    var geometry = new THREE.CircleGeometry(1, 100)
```

{% asset_img 5.png Markdown Preview Enhanced %}

好了，还剩两个参数，分段起始角度thetaStart（默认0）和扇区中心角大小thetaLength（默认2 * PI），两个参数实际上就决定了构成图形的这些三角面从何处开始排布以及排布到何处结束。

直观的例子，画一个四分之一圆：

```js
    var geometry = new THREE.CircleGeometry(1, 100, 0, Math.PI / 2)
```

{% asset_img 6.png Markdown Preview Enhanced %}

坐标的0度指的是X轴正方向，而扇区中心角逆时针方向增大（三角面排布为逆时针方向），即Y轴正方向90度，X轴负方向180度，Y轴负方向270度。

画个半圆就更简单啦~

>另外注意一点，所有三角面总是平均分配thetaStart和thetaLength构成的部分，不管是不是完整的2 * PI

*****

## 圆锥体

与上面的圆形一样，这个圆锥体准确应该称为**锥体**

ConeGeometry(radius : Float, height : Float, radialSegments : Integer, heightSegments : Integer, openEnded : Boolean, thetaStart : Float, thetaLength : Float)

radius — 圆锥底部的半径，默认值为1。
height — 圆锥的高度，默认值为1。

radialSegments — 圆锥侧面周围的分段数，默认为8。
heightSegments — 圆锥侧面沿着其高度的分段数，默认值为1。

openEnded — 一个Boolean值，指明该圆锥的底面是开放的还是封顶的。默认值为false，即其底面默认是封顶的。

thetaStart — 第一个分段的起始角度，默认为0。（three o'clock position）
thetaLength — 圆锥底面圆扇区的中心角，通常被称为“θ”（西塔）。默认值是2*Pi，这使其成为一个完整的圆锥。

我们对比*圆形*来看*圆锥体*，*圆锥*的底面就是上一节说的*圆形*，一模一样，*圆形*可以看作一个height为0的*圆锥*（当然height最小值为1）

也就是说*圆锥体*的底面是一个多边形，radius相当于*圆形*的radius，radialSegments相当于*圆形*的segments（这里最小值可以为2，此时*圆锥*表现为一个空间上的三角形），thetaStart和thetaLength也跟*圆形*的定义一样（当然他们也会影响侧面）。

height指*圆锥*高度，heightSegments指*圆锥*高度上的分段数，很好理解。

openEnded表示底面是否开放，即底面是否绘制，开放则不绘制底面

>注意：thetaLength不为2*PI时，底面与侧面之间不会自动封闭，也就是说绘制出的只有圆锥面（底面和侧面），而不是一个圆锥实心体

```js
    var geometry = new THREE.ConeGeometry(1, 2, 100, 1, false, 0, 2 * Math.PI)
```

{% asset_img 7.png Markdown Preview Enhanced %}

*****

## 圆柱体

与上面两种几何体一样，*圆柱体*实际上应该被称为**棱柱体**更确切。

他都两个底面是两个*圆形*

CylinderGeometry(radiusTop : Float, radiusBottom : Float, height : Float, radialSegments : Integer, heightSegments : Integer, openEnded : Boolean, thetaStart : Float, thetaLength : Float)

radiusTop — 圆柱的顶部半径，默认值是1。
radiusBottom — 圆柱的底部半径，默认值是1。

height — 圆柱的高度，默认值是1。

radialSegments — 圆柱侧面周围的分段数，默认为8。
heightSegments — 圆柱侧面沿着其高度的分段数，默认值为1。

openEnded — 一个Boolean值，指明该圆锥的底面是开放的还是封顶的。默认值为false，即其底面默认是封顶的。

thetaStart — 第一个分段的起始角度，默认为0。（three o'clock position）
thetaLength — 圆柱底面圆扇区的中心角，通常被称为“θ”（西塔）。默认值是2*Pi，这使其成为一个完整的圆柱。

radiusTop和radiusBottom分别表示两个面的半径，后面的参数与*圆锥体*一致，理解了*圆形*和*圆锥*，**棱柱体**就很好理解了

>我们看到radialSegments只有一个参数，说明顶部和底部圆形的分段数永远是一致的，不管半径如何，openEnded也同时控制顶面和底面（棱柱体同样是空心的）

标准圆柱：

```js
    var geometry = new THREE.CylinderGeometry(1, 1, 2, 100, 1, false, 0, 2 * Math.PI)
```

{% asset_img 8.png Markdown Preview Enhanced %}

大顶面的六棱柱：

```js
    var geometry = new THREE.CylinderGeometry(2, 1, 2, 6, 1, false, 0, 2 * Math.PI)
```

{% asset_img 9.png Markdown Preview Enhanced %}

>可以想象用CylinderGeometry创建一个立方体是很容易的事，只不过立方体的两个面必须是正方形

*****

## 十二面几何体

DodecahedronGeometry(radius : Float, detail : Integer)

radius — 十二面体的半径，默认值为1。
detail — 默认值为0。将这个值设为一个大于0的数将会为它增加一些顶点，使其不再是一个十二面体。

参数很简单，创建的结果也就是一个十二面体

```js
    var geometry = new THREE.DodecahedronGeometry()
```

{% asset_img 10.png Markdown Preview Enhanced %}

唯一可说的就是增大detail可以增加顶点，直至其近似一个球体

```js
    var geometry = new THREE.DodecahedronGeometry(1, 3)
```

{% asset_img 11.png Markdown Preview Enhanced %}

>注意：detail参数控制的顶点增长倍率极高，所以不要设太大的值，否则分分钟浏览器崩溃，猜测是顶点过多，显卡或者渲染器承受不了

*****

## 十二面几何体（三角面）

IcosahedronGeometry(radius : Float, detail : Integer)

radius — 二十面体的半径，默认为1。
detail — 默认值为0。将这个值设为一个大于0的数将会为它增加一些顶点，使其不再是一个二十面体。当这个值大于1的时候，实际上它将变成一个球体。

与上面一种几何体几乎一样，但是上面一种十二面几何体每个面是五边形，IcosahedronGeometry每个面是三角形。

*****

## 八面几何体

OctahedronGeometry(radius : Float, detail : Integer)

radius — 八面体的半径，默认值为1。
detail — 默认值为0，将这个值设为一个大于0的数将会为它增加一些顶点，使其不再是一个八面体。

每个面是三角形

*****

## 四面几何体

TetrahedronGeometry(radius : Float, detail : Integer)

radius — 四面体的半径，默认值为1。
detail — 默认值为0。将这个值设为一个大于0的数将会为它增加一些顶点，使其不再是一个四面体。

每个面是三角形

*****

## 平面几何体

PlaneGeometry(width : Float, height : Float, widthSegments : Integer, heightSegments : Integer)

width — 平面沿着X轴的宽度。默认值是1。
height — 平面沿着Y轴的高度。默认值是1。
widthSegments — （可选）平面的宽度分段数，默认值是1。
heightSegments — （可选）平面的高度分段数，默认值是1。

就是绘制一个平面（长方形）：

```js
    var geometry = new THREE.PlaneGeometry(2, 1)
```

{% asset_img 12.png Markdown Preview Enhanced %}

*****

## 多面几何体

多面体是在三维空间中具有一些平面的立体图形。这个类将一个顶点数组投射到一个球面上，之后将它们细分为所需的细节级别。

通俗来说就是：一堆顶点 + 一个索引数组将顶点结合为三角面 + 细分细节 = 多面几何体

PolyhedronGeometry(vertices : Array, indices : Array, radius : Float, detail : Integer)

vertices — 一个顶点Array（数组）：[1,1,1, -1,-1,-1, ... ]。
indices — 一个构成面的索引Array（数组）， [0,1,2, 2,3,0, ... ]。

radius — Float - 最终形状的半径。
detail — Integer - 将对这个几何体细分多少个级别。细节越多，形状就越平滑。

其中vertices为顶点数组，indices为索引数组，就是这个数组定义顶点如何构成三角面，radius决定几何体大小，detail与十二面几何体中的detail类似，值越大，插入的顶点越多。

```js
    var verticesOfCube = [
        -1,-1,-1,    1,-1,-1,    1, 1,-1,    -1, 1,-1,
        -1,-1, 1,    1,-1, 1,    1, 1, 1,    -1, 1, 1,
    ];

    var indicesOfFaces = [
        2,1,0,    0,3,2,
        0,4,7,    7,3,0,
        0,1,5,    5,4,0,
        1,2,6,    6,5,1,
        2,3,7,    7,6,2,
        4,5,6,    6,7,4
    ];

    var geometry = new THREE.PolyhedronGeometry( verticesOfCube, indicesOfFaces, 1, 0 );
```

{% asset_img 13.png Markdown Preview Enhanced %}

将detail增大，将接近一个球体

```js
    var geometry = new THREE.PolyhedronGeometry( verticesOfCube, indicesOfFaces, 1, 3 );
```

{% asset_img 11.png Markdown Preview Enhanced %}

这是一个非常灵活的几何体结构，相应的创建起来也更麻烦。（理论上可以用来定义各种形状）

*****

## 球体

上面看了那么多几何体都可以生成球体，threejs提供了一个球体的构造函数，准确来说，这是一个球面的构造函数，因为我们可以通过控制参数简单生成各种球面片段。

SphereGeometry(radius : Float, widthSegments : Integer, heightSegments : Integer, phiStart : Float, phiLength : Float, thetaStart : Float, thetaLength : Float)

radius — 球体半径，默认为1。

widthSegments — 水平分段数（沿着经线分段），最小值为3，默认值为8。
heightSegments — 垂直分段数（沿着纬线分段），最小值为2，默认值为6。

phiStart — 指定水平（经线）起始角度，默认值为0。。
phiLength — 指定水平（经线）扫描角度的大小，默认值为 Math.PI * 2。

thetaStart — 指定垂直（纬线）起始角度，默认值为0。
thetaLength — 指定垂直（纬线）扫描角度大小，默认值为 Math.PI。

该几何体是通过扫描并计算围绕着Y轴（水平扫描）和X轴（垂直扫描）的顶点来创建的。 因此，不完整的球体（类似球形切片）可以通过为phiStart，phiLength，thetaStart和thetaLength设置不同的值来创建， 以定义我们开始（或结束）计算这些顶点的起点（或终点）。

```js
    var geometry = new THREE.SphereGeometry(1, 15, 15);
```

{% asset_img 11.png Markdown Preview Enhanced %}

*****

## 圆环

相比于*圆形*，只是多了个内外半径之分而已（当然还要直径上的分段数），准确来说，看上去像个**多边环**

RingGeometry(innerRadius : Float, outerRadius : Float, thetaSegments : Integer, phiSegments : Integer, thetaStart : Float, thetaLength : Float)

innerRadius — 内部半径，默认值为0.5。
outerRadius — 外部半径，默认值为1。

thetaSegments — 圆环的分段数。这个值越大，圆环就越圆。最小值为3，默认值为8。
phiSegments — 最小值为1，默认值为8。

thetaStart — 起始角度，默认值为0。
thetaLength — 圆心角，默认值为Math.PI * 2。

>内外部的分段数是一致的

```js
    var geometry = new THREE.RingGeometry();
```

{% asset_img 14.png Markdown Preview Enhanced %}

>内外环半径都不能为0，为0表现默认值效果

*****

## 圆环几何体

TorusGeometry(radius : Float, tube : Float, radialSegments : Integer, tubularSegments : Integer, arc : Float)

radius - 圆环的半径，从圆环的中心到管道（横截面）的中心。默认值是1。
tube — 管道的半径，默认值为0.4。

radialSegments — 圆环的分段数，默认值为8，最小值为2，为2时是一个面。
tubularSegments — 管道的分段数，默认值为6，最小值为3。

arc — 圆环的中心角（单位是弧度），默认值为Math.PI * 2，决定了管道的长度。

```js
    var geometry = new THREE.TorusGeometry();
```

{% asset_img 14.png Markdown Preview Enhanced %}

*****

## 圆环扭结几何体

创建一个圆环扭结，其特殊形状由一对互质的整数，p和q所定义。如果p和q不互质，创建出来的几何体将是一个环面链接。

TorusKnotGeometry(radius : Float, tube : Float, tubularSegments : Integer, radialSegments : Integer, p : Integer, q : Integer)

radius - 圆环的半径，默认值为1。
tube — 管道的半径，默认值为0.4。
tubularSegments — 管道的分段数量，默认值为64。
radialSegments — 横截面分段数量，默认值为8。
p — 这个值决定了几何体将绕着其旋转对称轴旋转多少次，默认值是2。
q — 这个值决定了几何体将绕着其内部圆环旋转多少次，默认值是3。

*****

下面的几种几何体后续单独深入学习（由于涉及其他知识点）

## 车削几何体

创建具有轴对称性的网格，比如花瓶。车削绕着Y轴来进行旋转。

LatheGeometry(points : Array, segments : Integer, phiStart : Float, phiLength : Float)

points — 一个Vector2对象数组。每个点的X坐标必须大于0。
segments — 要生成的车削几何体圆周分段的数量，默认值是12。
phiStart — 以弧度表示的起始角度，默认值为0。
phiLength — 车削部分的弧度（0-2PI）范围，2PI将是一个完全闭合的、完整的车削几何体，小于2PI是部分车削。默认值是2PI。

*****

## 边缘几何体

EdgesGeometry( geometry : Geometry, thresholdAngle : Integer )

geometry — 任何一个几何体对象。
thresholdAngle — 仅当相邻面的法线之间的角度（单位为角度）超过这个值时，才会渲染边缘。默认值为1。

>暂不深入

*****

## 网格几何体

这个类可以被用作一个辅助物体，来对一个Geometry以线框的形式进行查看。

WireframeGeometry( geometry : Geometry )

它是bufferGeometry的子类

*****

## 挤压几何体

将一个二维形状挤压成三维几何体

ExtrudeGeometry(shapes : Array, options : Object)

shapes — 形状或者一个包含形状的数组。
options — 一个包含有下列参数的对象。

>暂不深入

*****

## 参数化几何体

ParametricGeometry(func : Function, slices : Integer, stacks : Integer)

>暂不深入

*****

## 形状几何体

ShapeGeometry(shapes : Array, curveSegments : Integer)

shapes — 一个单独的shape，或者一个包含形状的Array。
curveSegments - Integer - 每一个形状的分段数，默认值为12。

>操作起来比较麻烦，后面深入学习

*****

## 文本几何体

TextGeometry(text : String, parameters : Object)

text — 将要显示的文本。
parameters — 包含有下列参数的对象

>后面单独学习

*****

## 管道几何体

TubeGeometry(path : Curve, tubularSegments : Integer, radius : Float, radialSegments : Integer, closed : Boolean)

path — Curve - 一个由基类Curve继承而来的路径。
tubularSegments — Integer - 组成这一管道的分段数，默认值为64。
radius — Float - 管道的半径，默认值为1。
radialSegments — Integer - 管道横截面的分段数目，默认值为8。
closed — Boolean 管道的两端是否闭合，默认值为false。

>后面学习

## 总结

以上就是绝大部分几何体，个人认为使用难度不高，熟练灵活运用是关键。

部分几何体没有详细介绍，后面学习过程中可能掺杂学习。
