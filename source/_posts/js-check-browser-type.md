---
title: js判断浏览器类型
date: 2019-10-03 13:03:13
tags: 
- js
- 判断浏览器类型
categories: 前端
---

判断浏览器类型，可以用于兼容，比如创建ajax请求
<!-- more -->

#### 不考虑版本的区分浏览器
```js
    <script>
        function checkBrowser() {
            let explorer = window.navigator.userAgent;

            if (!!window.ActiveXObject || 'ActiveXObject' in window) {
                // window.ActiveXObject的作用是用来判断浏览器是否支持ActiveX控件
                // ActiveXObject在以下文档模式中受支持：Quirks、Internet Explorer 6 标准、Internet Explorer 7 标准、Internet Explorer 8 标准、Internet Explorer 9 标准、Internet Explorer 10 标准和 Internet Explorer 11 标准

                // 区分具体的ie版本
                // let reIE = new RegExp("MSIE (\\d+\\.\\d+);");
                // reIE.test(explorer);
                // let fIEVersion = parseFloat(RegExp["$1"]);
                // IE6 = fIEVersion == 6.0;
                // IE7 = fIEVersion == 7.0;
                // IE8 = fIEVersion == 8.0;
                console.log('ie');
            } else if (explorer.indexOf('Firefox') !== -1) {
                console.log('Firefox');
            } else if (explorer.indexOf('Chrome') !== -1) {
                console.log('Chrome');
            } else if (explorer.indexOf('Safari') !== -1) {
                console.log('Safari');
            }
        }
    </script>
```

#### 创建ajax
- 新建XMLHttprequest的对象，ie只有11以上才行，而且还是部分支持。对于ie，我们可以通过ActiveXObject来实现

```js
function createXmlHttpRequest(){  
    var xmlHttp ;  
    try{  
       //FF,Opera,Safari  
       xmlHttp = new XMLHttpRequest();  
    }catch(e){  
       try{  
           //支持IE6.0+  
            xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");  
       }catch(e){  
           try{  
              //支持IE5.5+  
              xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");  
           }catch(e){  
              alert("提示: 您的浏览器暂时不支持Ajax请求!");  
              return false;  
           }  
       }  
    }  

    return xmlHttp;  
} 
```