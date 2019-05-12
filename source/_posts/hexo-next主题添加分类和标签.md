---
title: hexo next主题添加分类和标签
date: 2019-05-12 11:46:36
categories: hexo
tags: 
    - hexo
    - next
---

## 添加分类

1. 新建一个分类页面

        $ hexo new page categories

2. `source`文件夹下有了`categories/index.md`，可以编辑，新增`type`属性`categories`

        title: categories
        date: 2019-05-12 11:41:32
        type: categories

3. 打开next主题下的`_config.yml`，找到`menu`，将`categories: /categories/ || th`注释去掉，没有的话添加
4. 写文章时归入分类，只需在文章顶部标题部分加上`categories`即可自动创建分类名并将文章归入分类中

        title: hexo next添加分类
        categories: hexo

## 添加标签

1. 新建一个标签页面

        $ hexo new page tags

2. `source`文件夹下有了`tags/index.md`，可以编辑，新增`type`属性`tags`

        title: tags
        date: 2019-05-12 11:57:08
        type: tags

3. 打开next主题下的`_config.yml`，找到`menu`，将`tags: /tags/ || tags`注释去掉，没有的话添加
4. 写文章时归入分类，只需在文章顶部标题部分加上`tags`即可自动创建分类名并将文章归入分类中

        title: hexo next添加标签
        tags:
            - hexo
            - next

***

以上~