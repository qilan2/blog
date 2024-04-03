---
title: JavaScript之防抖动和节流以及使用场景
cover: https://picx.zhimg.com/70/v2-ba837b08abda84af95651ba2604cf393_1440w.avis?source=172ae18b&biz_tag=Post
---

## 一. 函数防抖动(debounce)

在事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时。

```
function debounce(func, time) {
  var timeout;
  return function() {
    var _this = this;
    var args = arguments;
    //清除定时器
    clearTimeout(timeout);
    timeout = setTimeout(function() {
      func.apply(_this, args);
    }, time);
  };
}

function scrollEvent() {
  var scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop; //滚动高度
  var viewPortHeight = window.innerHeight || document.documentElement.clientHeight; //可视区的高度
  var docHeight = document.documentElement.scrollHeight; //document.documentElement.offsetHeight 整个网页文档的高度
  if (scrollTop + viewPortHeight > docHeight - 20) {
    console.log('滚动到底部了');
  }
}
var optimize = debounce(scrollEvent, 200);
document.addEventListener('scroll', function() {
  //停止滚动之后开始计算
  optimize();
});
```



### 使用场景

- 监听滚动事件判断是否到页面底部自动加载更多： 给 scroll 加了 debounce 后， 只有**用户停止滚动**后， **才会判断是否到了页面底部**；
- **模糊匹配或者模糊搜索**都可以使用防抖动,当用户输入完了开始调 ajax 获取数据(例如 keyup 事件)

## 二.节流

**规定在一个单位时间内**，只能**触发一次函数**。如果这个**单位时间**内触发多次函数，只有**一次生效**。

#### 节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

**1.时间戳**

第一种使用时间戳(第一次会立即执行,在 wait 时间内在执行是无效的)

```
  function throttle(func, wait){
    var resulTime = 0;
    return function (){
      var new = + new Date(); //时间戳
      var _this = this;
      vat args = arguments;
      // now - resulTime只有大于wait才会执行,在wait时间内只执行一次
      if(now - resulTime>wait){
        func.apply(_this,args);
        resulTime = now;
      }
    }
  }
```



**2.设置定时器**

第二种设置定时器(时间不会立即执行,在 wait 时间后开始执行)

```
function throttle(func, wait) {
  var timeout;
  return function() {
    var _this = this;
    var args = arguments;
    if (!timeout) {
      timeout = setTimeout(function() {
        func.apply(_this, args);
        timeout = null;
      }, wait);
    }
  };
}
```



```
//使用方法
function scrollThrottle() {
  console.log('heyushuo');
}
var optimizeThrottle = throttle(scrollThrottle, 3000);
document.addEventListener('scroll', function() {
  optimizeThrottle();
});
```



### 总结两种方式的区别

第一种事件会立刻执行， 第二种事件会在 n 秒后第一次执行

### 使用场景

- 按一个按钮发送 AJAX,给 click 加了节流(throttle)用户单位时间内点击多次,也只会发送一次
- 使用场景其实没有特定一说还是根据具体的需求来选择是使用抖动还是使用节流