---
title: vue中标签绑定class的写法
date: 2019-08-02
tags:
- vue
categories: 前端
---

绑定class的形式比较多，都记录一下日常使用到的东西

<!-- more --->
#### 对象形式
```js 
    :class="{'class1': data1, 'class2': data2}" # data1和data2是变量
```

#### 三元运算符形式
还可以放进数组中
```js
    :class="条件 ? 'class1' : 'class2'"
```

#### 数组形式
对于同时存在固定类名和动态的可以使用数组形式

- 单纯的数组形式
```js
    :class="['class1', 'class2']" # 或者class="class1 class2"
```

- 数组结合三元运算符
```js
    :class="[条件 ? 'class1' : 'class2', 'class1']"
```

- 数组结合对象
```js
    :class="['class1', {'class2': data2}]"
```

- 数组结合对象和三元
```js
    :class="[条件 ? 'class1' : 'class2', 'class1', {'class3': data3}]"
```