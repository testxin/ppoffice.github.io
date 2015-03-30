title: "Ripple Button（涟漪按钮）原生JavaScript实现"
date: 2015-03-26 21:44:28
categories:
- Front-End
- JavaScript
tags:
- JavaScript
---
最近Android 5.0中Ripple Button比较火爆的流行起来，在Web前端中其实现的原理较为简单。本文将尝试通过不使用第三方JavaScript库的情况下结合HTML与CSS实现Web前端中的Ripple Button。
[点击测试](/demo/ripple-buttons.html)（该示例在Chrome 41.0和IE 9/10/11下测试通过。）

![](/thumbnails/t3.jpg)

<!--more-->

HTML和CSS部分代码比较简单：
```html
<!DOCTYPE html>
<html>
<head>
  <title>Ripple Buttons</title>
  <style type="text/css">
    /* 
      button作为容器，其position需要设置为absolute来承载ripple背景
      overflow:hidden用于掩盖ripple超出button的部分
     */
    button {
      position: relative;
      background-color: transparent;
      border: none;
      width: 120px;
      height: 36px;
      overflow: hidden;
      color: #fff;
      border-radius: 4px;
      outline: none;
    }
    /* 
      button中的div为按钮的ripple背景，由JavaScript动态生成
     */
    button > div {
      position:absolute;
      background-color: rgba(255,255,255,0.3);
      border-radius: 50%;
    }
    .success {background-color: #04A43B;}
    .info {background-color: #0C9AF2;}
    .danger {background-color: #dd0d32;}
    .long {height: 200px; width: 90px;}
  </style>
</head>
<body>
  <button class="success">Click Me!</button>
  <button class="info">Click Me!</button>
  <button class="danger long">Click Me!</button>
</body>
</html>
```

Ripple Button的结构如下图所示：
![](/images/ripple-button-proto.png)
首先我们需要获得用户在button中点击的位置。可以采用onclick事件的offsetX和offsetY来获得用户点击位置相对于被点击的元素的偏移距离，即图中的圆心(x,y)。然后，我们假设按钮ripple背景的半径为Radius，则背景的top和left值可用下面的公式计算：
> top = y - Radius; left = x - Radius;

在计算出ripple的位置之后，下一步需要计算出ripple的最大半径。其最大半径应该满足无论在按钮中的哪一个位置发生点击（也即是无论圆的圆心在何处），都能满足动画完成时按钮的所有部分都被ripple覆盖。所以取圆心到达按钮边界最远的距离为ripple的最大半径（也就是圆心到按钮四个角中最远的距离），计算方法如下：
> maxRadiusX = max(buttonWidth - x, x);
> maxRadiusY = max(buttonHeight - y, y);
> maxRadius = sqrt(maxRadiusX^2 + maxRadiusY^2);

现在知道了ripple背景的位置和最大半径（maxRadius），剩下的工作就只要让圆的半径在一定的时间内从零增长到maxRadius，产生动画效果。在JavaScript中，可以使用setInterval来完成定时重复执行的任务。
```js
setInterval(function, interval);
```
setInterval函数包含两个参数，第一个是你需要执行的函数，另一个是定时的时间间隔（单位为ms）。我们只需每隔很小一段时间将ripple的半径增加一定值，就可以实现ripple的动态变大效果了。但是需要注意的是，当ripple的半径达到最大值时，需要使用clearInterval来取消定时器来终止动画。
最终，我们将整个动画事件绑定到button的mousedown事件上，这样当用户按下按钮时，按钮上就会产生涟漪效果。同理，我们可以在button的mouseup事件中绑定ripple的消失动画。Ripple的消失动画同样通过setInterval来完成。每隔一定时间，ripple的透明度就会减少一定值，直到ripple完全透明。

完整的JavaScript代码如下：
```js
var buttons = document.getElementsByTagName('button'), speed = 8;
for (var i = 0; i < buttons.length; i++) {
  buttons[i].onmousedown = function (e) {
    var e = e || window.event,
        offsetX = e.offsetX,
        offsetY = e.offsetY,
        radiusX = (this.offsetWidth - offsetX > offsetX?this.offsetWidth - offsetX:offsetX),
        radiusY = (this.offsetHeight - offsetY > offsetY?this.offsetHeight - offsetY:offsetY),
        offsetSize = Math.sqrt(Math.pow(radiusX,2) + Math.pow(radiusY,2)),
        inner = document.createElement('div');
   this.appendChild(inner);
    var interval = setInterval(function () {
      inner.style.width = (inner.offsetWidth + speed) + 'px';
      inner.style.height = (inner.offsetWidth + speed) + 'px';
      inner.style.left = (offsetX - inner.offsetWidth/2) + 'px';
      inner.style.top = (offsetY - inner.offsetHeight/2) + 'px';
      inner.style.opacity = inner.offsetWidth/offsetSize/2;
      if(inner.offsetWidth >= offsetSize*2 && inner.offsetHeight >= offsetSize*2) {
        clearInterval(interval);
      }
    }, 10);
  }
  buttons[i].onmouseup = function (e) {
    var e = e || window.event,
        this_ = this,
        inner = this.children[0];
    var interval = setInterval(function () {
      inner.style.opacity = inner.style.opacity - speed/this_.offsetWidth;
      if(inner.style.opacity <= 0) {
        this_.removeChild(inner);
        clearInterval(interval);
      }
    }, 10);
  }
};
```
[点击测试](/demo/ripple-buttons.html)