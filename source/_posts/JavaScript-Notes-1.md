title: "JavaScript杂记（一）"
date: 2015-03-16 09:13:36
categories:
- Front-End
- JavaScript
tags:
- JavaScript
---
![](/thumbnails/t2.jpg)
以下摘自《JavaScript 权威指南（第6版）》
<!-- more -->
* 如果当前语句和下一行语句无法合并解析，JavaScript则在第一行后填补分号。除了：①涉及return，break和continue语句，如果这三个关键字后紧跟着换行，JavaScript则会在换行处填补分号。
  ```javascript
  return
  true;
  ```
  JavaScript会解析成

  ```javascript
  return;true;
  ```
  而代码本意是

  ```javascript
  return true;
  ```

  ②“++”“--”运算符。他们可以作为表达式的前缀，也可以作为表达式的后缀。如果将其用作后缀表达式，他和表达式应当在同一行。否则行尾将填补分号，同时“++”“--”将作为下一行代码的前缀操作符并与之一起解析。
  ```javascript
  x
  ++
  y
  ```
  这段代码将被解析为“x;++y”；而不是“x++;y”。

* 下溢（underflow）是当运算结果无限接近于零并比JavaScript能表示的最小值还小时的一种情况。这种情况下，JavaScript会返回0。负数下溢时会返回“负零”。零和负零几乎相等，除了下种情况：
  ```javascript
  var zero = 0;
  var negz = -0;
  1/zero === 1/negz // => false: 正无穷大和负无穷大不相等
  ```

* 被零整除在JavaScript中并不报错，而返回无穷大（Infinity）和负无穷大（-Infinity）。但是零除以零没有意义，结果返回一个非数字（not-a-number），即NaN。无穷大除以无穷大、给任意负数作开方运算或者算数运算符与不是数字或无法转换为数字的操作数一起使用时都会返回NaN。

* NaN与任何值都不相等，包括自身。无法通过x==NaN来判断x是否为NaN，但可以通过x!=x来判断，或者函数isNaN()。JavaScript中又一个类似函数为isFinite()，在参数部位NaN、Infinity和-Infinity时返回true。

* JavaScript中数字不能精确表述会带来一些问题。如：
  ```javascript
  var x = .3 - .2;
  var y = .2 - .1;
  x == y // => false: 两值不相等
  x == .1 // => false: .3 - .2 != .1
  y == .1 // => true: .2 - .1 == .1
  ```
  由于舍入误差，0.3和0.2之间的近似误差实际上并不等于0.2和0.1之间的近似误差。在JavaScript真实运行环境中，0.3 - 0.2 = 0.099 999 999 999 999 98。所以在涉及金融等计算时，最好使用“分”而不是“元”作为单位。

* 转义字符中，如果“\”与其后的字符不属于规定的转义字符，则忽略“\”。比如，“\#”和“#”等价。