---
title: 对PWA的一些思考
date: 2019-05-27 22:31:06
categories: js
tags: pwa
---

最近开始接触开发小程序，突然想到了之前做过的PWA，两者在某些设计思想方面还是有一些共性的，下面做一个总结~

## Why PWA

为什么google会推出pwa呢？首先来看下移动时代web有什么致命的限制：

1. 用户体验：web应用无论如何优化，如何提高首屏渲染性能，还是有一个网络加载的过程，这个过程导致用户需要延时等待，体验比Native App差很多
2. 用户留存：web app的入口一直是搜索引擎，再次进入这个应用的路径很麻烦，并且用户基本不会去记住网址，移动端输入网址的成本也极高，导致web上用户留存比App低很多，用户粘性也很低

移动`Web`可以触达的用户是`App`的三倍之多，但用户的留存时间和留存率却明显少于`App`，so，我们需要PWA

再来看下web相较App有什么优势，随便举几个例子：

1. 真正的一套代码，多端使用，相比flutter、weex等技术，很多人似乎忘记了web才是跨端的鼻祖，相比两套原生App代码，web的开发成本低很多
2. web天然的开放性导致PWA也是完全开放的，NA却是封闭的，内容无法被索引，没有天然的流量入口
3. App必须要下载才能使用，很多用户对动辄几十兆的App望而却步，并且很多场景下用户只是想使用一个简单的小功能而已，而web app打开即用，用完即走的特性完全满足了这种场景
4. App更新必须审核发版，并且用户端也需要更新，而web是动态更新，用户无感知更新过程
......

这些都是本人拥抱web的理由，哈哈哈哈~

PWA具有web优势，同时突破了web的限制，so，W3C选择了PWA

## what‘s PWA

官方说法：PWA（Progressive Web App渐进式web应用） 不是特指某一项技术，而是应用了多项技术的 Web App。其核心技术包括 App Manifest、Service Worker、Web Push、Credential Management API ，等等。其核心目标就是提升 Web App 的性能，改善 Web App 的用户体验。

实际上就是说PWA是一系列技术的组合，包含的技术就是上面巴拉巴拉一堆，而PWA优秀的一点就是它允许你根据具体需求场景和浏览器的支持程度选择这些技术中的一项或几项来构建你的PWA应用，因此可以说任何一个web应用都可以被改造成PWA，最终实现web app渐进增强的效果。

### App Shell

在了解PWA具体技术栈之前，先简单看下什么是App Shell，它实际上不算是一种技术，而是一种前端架构设计思想。

App“shell”是支持用户界面所需的最小的 HTML、CSS 和 JavaScript，如果离线缓存，可确保在用户重复访问时提供即时、可靠的良好性能。

理解：App shell实际上类似我们常说的首屏渲染，将核心应用基础架构和 UI 从动态数据中分离出来，即为”shell“，它是应用的骨架以及需要尽快展现给用户的最必要最简单的部分

应用本身符合App Shell模型对PWA的实践更为方便，App Shell 架构是构建 Progressive Web App 的一种方式。

### Service Worker

如何突破网络加载导致的用户体验问题，答案就是service worker

#### service worker是如何被造出来的呢？

由于javascript运行是单线程的，主线程中做一些复杂耗时的计算会导致线程阻塞、渲染卡顿等问题。w3c早早的洞察了这些问题，这个时候一个叫web worker的API就被创造出来了，主要目的就是分担主线程的任务，Web Worker 是脱离在主线程之外的，可以将web worker的运行结果通过postMessage传递给主线程，主线程监听onMessage来获取结果。问题似乎解决了，但web worker是临时的，网站关闭web worker也就结束了，每次都要重新运行计算复杂逻辑。有没有一个worker能一直存在，随时等待主线程调用呢？那就是service worker，service worker在web worker基础上增加了离线缓存的能力。

#### Service Worker 有以下功能和特性：

* 一个独立的 worker 线程，独立于当前网页进程，有自己独立的 worker context。
* 一旦被 install，就永远存在，除非被手动 unregister
* 用到的时候可以直接唤醒，不用的时候自动睡眠
* 可编程拦截代理请求和返回，缓存文件，缓存的文件可以被网页进程取到（包括网络离线状态）
* 离线内容开发者可控
* 能向客户端推送消息
* 不能直接操作 DOM
* 必须在 HTTPS 环境下才能工作
* 异步实现，内部大都是通过 Promise 实现

#### 使用

* Service Worker 的缓存机制是依赖 Cache API 实现的
* 依赖 HTML5 fetch API

1. 注册
要安装 Service Worker， 我们需要通过在 js 主线程（常规的页面里的 js ）注册 Service Worker 来启动安装，这个过程将会通知浏览器我们的 Service Worker 线程的 javaScript 文件在什么地方呆着。

```js
    if ('serviceWorker' in navigator) {
        window.addEventListener('load', function () {
            navigator.serviceWorker.register('/sw.js', {scope: '/'})
                .then(function (registration) {

                    // 注册成功
                    console.log('ServiceWorker registration successful with scope: ', registration.scope);
                })
                .catch(function (err) {

                    // 注册失败:(
                    console.log('ServiceWorker registration failed: ', err);
                });
        });
    }
```

代码执行完成之后，我们这就注册了一个 Service Worker，它工作在 worker context，所以没有访问 DOM 的权限。在正常的页面之外运行 Service Worker 的代码来控制它们的加载。
2. 安装
Service Worker 注册成功之后，浏览器中就有了一个属于自己web app的worker context，并且浏览器会立即尝试安装并激活它，install 事件我们会绑定在 Service Worker 文件中，在 Service Worker 安装成功后，install 事件被触发
在这里可以利用cache API做缓存的事

```js
    // 监听 service worker 的 install 事件
    this.addEventListener('install', function (event) {
        // 如果监听到了 service worker 已经安装成功的话，就会调用 event.waitUntil 回调函数
        event.waitUntil(
            // 安装成功后操作 CacheStorage 缓存，使用之前需要先通过 caches.open() 打开对应缓存空间。
            caches.open('my-test-cache-v1').then(function (cache) {
                // 通过 cache 缓存对象的 addAll 方法添加 precache 缓存
                return cache.addAll([
                    '/',
                    '/index.html',
                    '/main.css',
                    '/main.js',
                    '/image.jpg'
                ]);
            })
        );
    });
```

3. 监听请求
现在已经缓存资源了，接下来要使用这些资源，每次任何被 Service Worker 控制的资源被请求到时，都会触发 fetch 事件，Service Worker通过fetch代理请求来使用缓存

```js
    this.addEventListener('fetch', function (event) {
        event.respondWith(
            caches.match(event.request).then(function (response) {
                // 来来来，代理可以搞一些代理的事情

                // 如果 Service Worker 有自己的返回，就直接返回，减少一次 http 请求
                if (response) {
                    return response;
                }

                // 如果 service worker 没有返回，那就得直接请求真实远程服务
                var request = event.request.clone(); // 把原始请求拷过来
                return fetch(request).then(function (httpRes) {

                    // http请求的返回已被抓到，可以处置了。

                    // 请求失败了，直接返回失败的结果就好了。。
                    if (!httpRes || httpRes.status !== 200) {
                        return httpRes;
                    }

                    // 请求成功的话，将请求缓存起来。
                    var responseClone = httpRes.clone();
                    caches.open('my-test-cache-v1').then(function (cache) {
                        cache.put(event.request, responseClone);
                    });

                    return httpRes;
                });
            })
        );
    });
```

4. Service Worker 版本更新
如果希望在有了新版本时，所有的页面都得到及时自动更新怎么办呢？可以在 install 事件中执行 self.skipWaiting() 方法跳过 waiting 状态，然后会直接进入 activate 阶段。接着在 activate 事件发生时，通过执行 self.clients.claim() 方法，更新所有客户端上的 Service Worker。

```js
    // 安装阶段跳过等待，直接进入 active
    self.addEventListener('install', function (event) {
        event.waitUntil(self.skipWaiting());
    });

    self.addEventListener('activate', function (event) {
        event.waitUntil(
            Promise.all([

                // 更新客户端
                self.clients.claim(),

                // 清理旧版本
                caches.keys().then(function (cacheList) {
                    return Promise.all(
                        cacheList.map(function (cacheName) {
                            if (cacheName !== 'my-test-cache-v1') {
                                return caches.delete(cacheName);
                            }
                        })
                    );
                })
            ])
        );
    });
```

总之，service worker帮助我们实现了离线缓存和版本管理，如果缓存了App Shell，那就会出现本地秒开站点的效果了。

### Web App Manifest

PWA 提供了 manifest.json 配置文件，可以让开发者自定义添加至桌面时的图标、显示名称、启动方式等等信息，并提供 API 方便开发者管理网络应用安装横幅，让用户可以方便快捷地将您的站点添加到主屏幕中。

manifest.json

```json
    {
        "short_name": "短名称",
        "name": "这是一个完整名称",
        "icon": [
            {
                "src": "icon.png",
                "type": "image/png",
                "sizes": "48x48"
            }
        ],
        "start_url": "index.html"
    }
```

html

```html
    <link rel="manifest" href="path-to-manifest/manifest.json">
```

### Push Notification

Push Notification 其实是两个 API 的结合， Notification API 和 Push API 。

Notification API 提供了开发者可以给用户发送通知的能力，包括申请显示通知权限，发起通知，以及定制通知的类型等等

Push API，则是让服务器能够向用户发送离线消息，即使用户当前并没有打开你的页面，甚至没有打开浏览器。浏览器在接到消息推送时，会唤醒对应的 Service Worker，并抛出 push事件，开发者接收到事件之后调用 Notification API 弹出通知，这就完成了整个接受和展示的流程。

web push在国内的支持度极低，它还需要浏览器厂商提供消息推送服务，Chrome 也因为其依赖的 FCM/GCM 无法访问而导致 Web Push 无法使用。感兴趣的同学可以自己研究下

### 其他

除了 Web App Manifest、Service Worker、Push API 这三个关键的技术外，PWA 还包含很多优化的准则，如Credential Management API，目的就是采用各种技术创造用户体验非常好的 Web App。

## PWA与小程序

实际上各厂商推出小程序也是解决了文章开始提到的两个web致命限制：用户体验和用户留存，同时目前用户 80% 的时间被 Top3 的超级 App 占据，对于站点来说，App应用分发成本也因此越来越高，但是小程序依托于这些超级App，反而具有了天然的流量来源，可能这就是小程序为什么越来越火的原因吧！

小程序架构设计上与PWA的相似点：

1. 开发都是使用web技术
2. 离线缓存应用
3. 方便的入口
4. 可被检索
5. 安全性

区别也很明显：PWA 现有的所有技术都是遵循 W3C 的标准，完全开放，而小程序是各厂商各自为营，暂时没有业界开发标准，但是小程序也提供了一些操作原生的能力、云开发能力等，这是PWA不具有的。