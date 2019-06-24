---
title: Taro初探
date: 2019-06-24 10:23:38
categories: 小程序
tags: 小程序
---

## 小程序开发框架

自2019年1月9日微信小程序发布以来，出现了很多开发框架，目前社区流行的框架也有很多，下面是一个简单的对比

### 早期的wepy和mpvue

wepy：微信官方提供的开发框架，仅支持微信小程序，基于微信小程序template实现组件化，vue系语法

mpvue：类vue框架，在vue上有所删减，针对微信小程序，同样是将vue模板编译成小程序wxml模板（template）实现组件化

### 新兴的跨端框架

随着各大厂商陆续推出自家的小程序，各种号称`write once, run anywhere`的框架也开始出现

taro：由京东凹凸实验室团队开发的跨端框架，支持将taro代码编译成多端可用的代码，目前支持微信小程序、支付宝小程序、百度小程序、头条小程序、h5、app端，6月份发布的3.0还支持qq小程序和轻应用。框架实现了绝大部分微信小程序组件和api，基于小程序component实现组件化，react系语法。

uni-app：目前社区活跃度最高的框架，与taro一样支持四端小程序和h5以及app，提供条件编译模型，vue系语法

chameleon：新出的框架，跨端支持度和社区都不太成熟

## taro

### taro相较微信小程序原生开发的优势

1. 多端可用，这是最大的优势
2. 前端项目工程化，支持各种编译器，更简单的引入npm包管理
3. 可使用Redux或Mobx做状态管理
4. 开发体验优于原生开发

### taro安装和使用

官方提供脚手架cli

创建新项目：

```js
  # 安装
  $ npm install -g @tarojs/cli
  $ taro init myApp
```

打包到各端:

```js
  # 微信小程序
  $ npm run dev:weapp
  $ npm run build:weapp
  
  # 百度小程序
  $ npm run dev:swan
  $ npm run build:swan

  # 支付宝小程序
  $ npm run dev:alipay
  $ npm run build:alipay

  # 字节跳动小程序
  $ npm run dev:tt
  $ npm run build:tt

  # h5
  $ npm run dev:h5
  $ npm run build:h5

  # rn
  $ npm run dev:rn
  $ npm run build:rn
```

### 框架

* 语法
整个语法风格都是React的体系，包括component以及JSX，但是Taro有自己定义的很多语法细节，严格的限制导致一个React项目可能并非是一个合格的Taro工程，因此有一定的学习成本。

* 生命周期
taro页面和app的生命周期都是React生命周期的形式，但是分别对应微信小程序生命周期的不同阶段，例如ComponentDidMount对应小程序的onLaunch

```js
  import Taro, { Component } from '@tarojs/taro'
  import { View, Text } from '@tarojs/components'
  import './index.scss'

  export default class Index extends Component {
    config = {
      navigationBarTitleText: '首页'
    }

    componentWillMount () { }

    componentDidMount () { }

    componentWillUnmount () { }

    componentDidShow () { }

    componentDidHide () { }

    render () {
      return (
        <View className='index'>
          <Text>1</Text>
        </View>
      )
    }
  }
```

* 配置
taro全局配置和页面配置及配置项均对应微信小程序中的.json文件，形式则为Component中config配置项

```js
  class App extends Component {
    // 项目配置
    config = {
      pages: [
        'pages/index/index',
        'pages/logs/logs'
      ]
    }
    ...
  }
```

* 事件机制
  1. 除了上述生命周期，页面上的事件如onReachBottom、onPageScroll等与小程序中表现基本一致
  2. 用户事件则与dom表现一致，只是有些许差别，如事件传参方式、绑定属性命名方式、以及不能通过 catchEvent 的方式阻止事件冒泡，必须明确的使用 stopPropagation
  3. 组件事件则使用`this.props.onMyEvent()`的形式触发

### 组件

taro组件化语法层面完全是react的组件形式（但不支持高阶组件等复杂写法），组件以函数形式或者继承Taro.Component的class来定义，render方法渲染JSX模板，对于小程序，组件将编译成component来进行管理

#### 组件内部状态state

react通过state来做组件的状态管理，表示组件的内部状态和数据，而taro组件state有点类似小程序中的data，可以通过setState（setData）来改变模板数据

#### 组件间通信

父组件通过props与子组件通信，子组件通过`this.props.onMyEvent()`的形式触发事件向父组件传递数据

taro也提供refs的方式来直接调用组件实例，但是不建议使用

#### 组件库

taro基于微信小程序组件库实现了一套与之对应的组件库，覆盖大部分的微信小程序组件，但是也缺少一些组件，例如直播类组件taro完全没有提供，另外组件库中组件的属性和事件都不如原生小程序丰富，且没有版本支持度标识，部分组件缺少较多属性。

实际的使用过程中发现很多组件都存在这样那样的使用问题，并且社区中对这些问题也并没有很好的解决方案，这点很蛋疼...

taro支持与原生小程序混写，因此部分组件问题理论上可以通过这种途径解决。

### API

taro的api绝大部分是根据微信小程序api实现的，只是把调用方式从`wx.`改成了`Taro.`，并且支持promise形式调用

部分微信新增的api，taro还没有同步实现，而部分类似相机帧之类使用较少的api，taro好像并没有实现的打算

### 实践

下面是一个简单的类首页taro实现，包含长列表上拉加载，用户交互和简单布局

[https://github.com/brightcd/taro-test](https://github.com/brightcd/taro-test)

个人感觉taro的开发体验要比小程序原生好太多，但是组件确实坑比较多！

## 总结

结合目前社区的状况来看，个人认为如果仅针对微信小程序开发，则可以选择原生或者框架，个人更倾向于原生，原因是框架有其局限性，而原生可以应对更多的复杂业务场景。如果有多端开发需求，则可以根据团队技术栈情况选择taro（react）或uni-app（vue）。
