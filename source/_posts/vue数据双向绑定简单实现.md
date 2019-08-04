---
title: vue数据双向绑定简单实现
date: 2019-08-03 12:32:34
categories: js
tags: 
    - vue
    - 数据绑定
---

不同于angular的脏检测机制，vue通过对象劫持来实现数据双向绑定

## 实现思路

1. 监听数据变化，通过Object.defineProperty API重写数据对象get、set存取器，数据变更时触发watcher中的队列回调
2. 注册watcher，watcher对象连接数据和UI，数据变更触发watcher的update更新视图
3. 编译模板，将dom和watcher绑定

## 上代码

dom结构

```html
    <div id="app">
        <input type="text" v-model="name">
        <h3 v-bind="name"></h3>
        <input type="text" v-model="testData1">
        <h3>{{ testData1 }}</h3>
        <input type="text" v-model="testData2">
        <h3>{{ testData2 }}</h3>
        <div>{{time}}</div>
        <div>{{sec}}</div>
    </div>
```

实现

```js
    // watcher
    let Watcher = function ($el, attr, my, k) {
        this.$el = $el;
        this.attr = attr;
        this.my = my;
        this.k = k;
        this.update();
    }

    Watcher.prototype.update = function () {
        this.$el[this.attr] = this.my._data[this.k];
    }


    // my
    let My = function (options = {}) {
        // 数据和要渲染的UI
        this._$el = document.querySelector(options.el);
        this._data = options.data;

        this.watcher = {};

        // 数据更新时处理回调任务
        this._observer(this._data);
        // 绑定UI和数据
        this._compile(this._$el);
    }

    // 实现数据监听
    My.prototype._observer = function (obj) {
        for (let key in obj) {
            if (obj.hasOwnProperty(key)) {
                // 监听数据更新的任务队列
                this.watcher[key] = {
                    _directives: []
                }
                let value = obj[key];
                let _watcher = this.watcher[key];
                Object.defineProperty(obj, key, {
                    configrable: true,
                    enumerable: true,
                    get () {
                        return value;
                    },
                    set (val) {
                        if (val !== value) {
                            value = val;
                            _watcher._directives.forEach((item) => {
                                item.update();
                            });
                        }
                    }
                })
            }
        }
    }

    // compile
    My.prototype._compile = function ($el) {
        let nodes = $el.children;
        for (let node of nodes) {
            if (node.children.length) {
                this._compile(node);
            }

            if (node.hasAttribute('v-model') && (node.tagName == 'INPUT' || node.tagName == 'TEXTAREA')) {
                let val = node.getAttribute('v-model');
                this.watcher[val]._directives.push(new Watcher(node, 'value', this, val));
                node.addEventListener('input', () => {
                    this._data[val] = node.value;
                });
            }

            if (node.hasAttribute('v-bind')) {
                let val = node.getAttribute('v-bind');
                this.watcher[val]._directives.push(new Watcher(node, 'innerHTML', this, val));
            }

            let reg = /\{\{\s*([^}]+\S)\s*\}\}/g, txt = node.textContent;
            if (reg.test(txt)) {
                node.textContent = txt.replace(reg, (match, val) => {
                    if (!this.watcher[val]) {
                        return txt;
                    }
                    this.watcher[val]._directives.push(new Watcher(node, 'innerHTML', this, val));
                    return this._data[val];
                })
            }
        }
    }
```

示例

```js
    let data = {
        testData1: 'Mini Vue',
        testData2: '双向绑定',
        name: 'JesBrian',
        time: ''
    }
    let test = new My({
        el: '#app', 
        data
    });

    setInterval(() => {
        data.time = Date.now();
    }, 1000);
```

以上，一个简单的双向数据绑定就实现啦，我们不需要重复造轮子，但是要知道轮子背后的原理和思想，引申一下，对象劫持也可以使用ES6 Proxy实现，甚至更好，整个设计中基于发布/订阅的模式其实在很多场景下都能使用哦~

编译部分的实现有瑕疵，以后有时间再改
