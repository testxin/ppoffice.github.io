title: "JavaScript杂记（二）"
date: 2015-03-29 22:47:10
categories:
- Front-End
- JavaScript
tags:
- JavaScript
---
![](/thumbnails/t4.jpg)
以下摘自《JavaScript 语言精粹》
<!-- more -->
* length属性的值是其数组的最大整数属性名加上1，但它不一定等于数组里的属性的个数：
  ```js
  var myArray = [];
  myArray.length            // 0

  myArray[1000000] = true;
  myArray.length            // 1000001
  // myArray只包含一个属性
  ```

* 判断对象是否为数组的好方法：
  ```js
  var is_array = function (value) {
    return Object.prototype.toString.apply(value) === '[object Array]';
  };
  ```

* Array的sort方法不能对一组数字进行正确排序：
  ```js
  var n = [4, 8, 15, 16, 23, 42];
  n.sort();
  // n = [15, 16, 23, 4, 42, 8]
  ```
  JavaScript的默认比较函数把要被排序的元素都视为字符串，所以得到了一个错的离谱的结果，解决方法如下：
  ```js
  n.sort(function (a, b) {
    return a - b;
  });
  ```

* 摩尔定律并不适用于电池。

* NaN是IEEE 754中定义的一个特殊的数量值，它表示的是`不是一个数字`，但是下面表达式返回的是`true`：
  ```js
  typeof NaN === 'number' // true
  ```
  所以判断是否为数字的最佳方法是：
  ```js
  var isNumber = function (value) {
    return typeof value === 'number' && isFinite(value);
  }
  ```

* 一门语言最糟糕的特性不是那些一看就知道很危险或者没有价值的特性，那些特性很容易被避免。最糟糕的特性就像带刺的玫瑰，它们是有用的，但也是危险的。

* 用圆括号把JSON文本括起来是一种避免JavaScript语法歧义的变通方案。
  ```js
  var myData = eval('(' + myJSONText + ')');
  ```