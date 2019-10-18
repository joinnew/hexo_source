---
title: curry
date: 2019-08-16
tags:
- js
categries: 前端
---

在数学和计算机科学中，柯里化是一种将使用多个参数的一个函数转换成一系列使用一个或多个参数的函数的技术。
<!-- more -->

举例来说：一个函数接受4个参数，进行柯里化之后，柯里化版本的函数接受一个或多个参数并返回接受下一个参数的函数。直到最后一个函数（通过判断接收的参数数量与原函数的形参数量相同来判断），会将前面接受到的参数应用到原普通函数中，并返回最终结果。

```javascript
    // 普通函数
    function (a, b, c, d, e) {
        // 操作
    }

    // 柯里化
    _fn(a)(b, c)(d)(e)之类的 

    // 但是他们返回的结果和普通函数一致
```

### 柯里化的用途
核心在于对函数参数的自由处理，本质是降低通用性，提高适用性。
- 如：对数据的校验，校验手机号、邮箱之类的，我们可能会封装一个校验函数，把校验规则和数据内容传递进去。**如果有多条数据，我们可能会调用多次这个函数，每次都需要把相同的校验规则再次传递**。柯里化可以把校验规则这块相同的参数部分复用。

### 如何封装柯里化工具函数
- 如何确定是否已经获取到足够的参数呢？
1.通过函数的 length 属性，形参的个数那么就是实际所需的参数个数
2.在调用柯里化工具函数时，指定所需的参数个数

```javascript
/**
 * 将函数柯里化
 * @param fn    待柯里化的原函数
 * @param len   所需的参数个数，默认为原函数的形参个数
 */
function curry(fn, len = fn.length) {
    // fn.length是指函数的形参数量
    return _curry.call(this, fn, len)
}

/**
 * 中转函数
 * @param fn    待柯里化的原函数
 * @param len   所需的参数个数
 * @param args  已接收的参数列表
 */
function _curry(fn, len, ...args) {
    return function (...params) {
        let _args = [...args, ...params];
        if(_args.length >= len){ // 参数已经全部获取到
            return fn.apply(this, _args);
        }else{
            return _curry.call(this, fn, len, ..._args)
        }
    }
}

```