---
title: reduce的使用
date: 2019-10-18 21:38:23
tags:
- js
categories: 前端
---

定义： reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右, **每个值都会进行一次处理 res 就是前一个值处理的结果**）开始缩减，最终计算为一个值。
语法： array.reduce(function(total, currentValue, currentIndex - 可选, arr - 可选), initialValue - 可选,**也可以是[], {}之类的默认值**)


<!-- more -->
- 用法一：数组的项进行叠加，得出最终相加的结果
```javascript
    // 基本数据数组
    const arr = [1, 2, 4, 5, 6, 9];
    let sum = arr.reduce((total, curVal) => total + curVal, 0)

    // 对象数组处理
    const arr1 = [
        {
            name: 'zs',
            age: 10
        },
        {
            name: 'ls',
            age: 20
        },
        {
            name: 'ww',
            age: 23
        }
    ]

    let sum = arr1.reduce((total, curVal) => {
        return total + curVal.age
    }, 0)

    // 或者
    let sum1 = arr1.reduce((total, { age }) => total + age, 0)
```

- 用法二：字符串中每个字符出现的次数
```javascript
    const str = '321323dsafwrwer**$234dsafghbh';

    const res = str.split('').reduce((res, cur) => {
        // 也是利用对象属性的唯一特性
        res[cur] ? res[cur]++ : res[cur] = 1;
        return res;
    }, {})
```

- 用法三：数组最大值
```javascript
    const arr = [4324, 43242, 545, 12, 890];

    const max = arr.reduce((res, cur) => {
        return res > cur ? res : cur
    })
```

- 用法四：从对象数组中提取出某个属性，将这个属性单独变成一个数组
```javascript
    const arr = [
        {
            city: '上海',
            num: 1000
        },
        {
            city: '北京',
            num: 2000
        },
        {
            city: '南京'，
            num: 903
        }
    ]

    const res = arr.reduce((total, { city }) => {
        total.push(city);
        return total;
    }, [])
```

- 用法五：对象数组去重
```javascript
        const arr = [
        {
            city: '上海',
            num: 1000
        },
        {
            city: '北京',
            num: 2000
        },
        {
            city: '南京',
            num: 903
        },
        {
            city: '北京',
            num: 12323
        }
    ]

    const isExistedData = {};
    const res = arr.reduce((res, cur) => {
        if (!isExistedData(cur.city)) {
            isExistedData[cur.city] = true;
            res.push(cur);
        }

        return res;
    }, [])
```

- 用法六：扁平一个多维数组
```javascript
    const arr = [[1, 2, 3, 4], [5, 6, 7], [12, 23]];

    const res = arr.reduce((res, cur) => res.concat(cur), []);
```