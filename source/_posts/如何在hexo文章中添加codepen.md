---
title: 如何在hexo文章中添加codepen
date: 2019-04-25 21:14:17
categories: hexo
tags:
    - hexo
    - codepen
---

对于程序员来说，在写文章时，有的时候需要插入一个代码块，Markdown插入的代码块还算比较好看，但是不够酷啊，流行的[CodePen](https://codepen.io/)在插入运行前端代码时就很ok，而hexo正好有插件，开心  ~

废话不多说

## 安装

使用npm安装插件

    $ npm install hexo-codepen --save

## 语法

    {% codepen userId|anonymous|anon slugHash theme [defaultTab [height [width]]] %}

## 生成的html

    <p data-height="265" data-theme-id="dark" data-slug-hash="bgjKKE" data-default-tab="css,result" data-user="CiTA" data-embed-version="2" data-pen-title="CSS sidebar toggle" class="codepen">See the Pen <a href="https://codepen.io/CiTA/pen/bgjKKE/">CSS sidebar toggle</a> by Silvestar Bistrović (<a href="https://codepen.io/CiTA">@CiTA</a>) on <a href="https://codepen.io">CodePen</a>.</p>
    <script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

看源码实际上是插入了一个iframe

## 参数使用

参数 | 值
- | -
userId | codepen的用户名
slugHash | 当前pen url上的hash值
theme | dark
defaultTab | css,result 默认展现的tab
height | 265
width | 默认100%，这个值应该由主题调整

插件源码也很简单：<https://github.com/maliMirkec/hexo-tag-codepen>

## 效果

{% codepen brightc wZRNNQ dark%}

***

是不是效果很酷啊，哈哈，enjoy it~