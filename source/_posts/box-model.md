---
title: box-model
date: 2019-10-02
tags: CSS
categories: 前端
---

盒模型一共有两种模式：W3C标准模式和IE怪异模式
W3C标准模式也就是标准盒模型
IE怪异模式也就是IE盒模型、怪异盒模型

<!-- more -->
#### 标准盒模型
<img src='http://pytyayr4p.bkt.clouddn.com/%E6%A0%87%E5%87%86%E7%9B%92%E6%A8%A1%E5%9E%8B' width=200>

- 在标准盒模型下，width和height是content的width和height。默认情况下，我们设置的width和height就是针对content区域的。
- 其元素框的总宽度 = width + padding(左右) + border(左右) + margin(左右)
- 其元素狂的总高度 = height + padding(上下) + border(上下) + margin(上下)
- 通常我们设置背景色，就是包含content、padding、border，如果border设置了自己的颜色，那就显示其设置的颜色

#### IE盒模型
<img src='http://pytyayr4p.bkt.clouddn.com/ie%E7%9B%92%E6%A8%A1%E5%9E%8B' width=200>

- 在该模型下，width和height表示的是 content的width和height + padding + border这几个部分，设置的width和height就是几个部分的宽高
- 其元素框的总宽度 = width + margin(左右)
- 其元素狂的总高度 = height + margin(上下)

#### 什么情况下触发这些模型
- 在文档首部加了doctype申明，即使用了标准盒模型，不加的话，则由浏览器自己决定使用什么模型，比如，ie 浏览器（<font color=red>通常说的“IE盒子模型”指的是IE5.5，不过也有的说是IE8以下的会这样</font>）中显示“ie盒子模型”，在 ff 浏览器中显示“标准 w3c 盒子模型”。

- 也可以通过css3的属性box-sizing来指定选择什么模型
