# BOM

浏览器对象模型，将浏览器内容看作是一个对象，包括内容：

- window
  - document
  - location
  - history
  - ...



## window 对象事件

### 窗口加载事件

window.onload = funciton(){} ：页面元素加载完成后触发（原始方式如果脚本写在页面元素前面的话会失效）

window.addEventListener('load' , function(){}) : 功能同上

上述方法会加载页面的全部素材，如果图片很多的时候可能会使得加载时间过长，可以使用：

```
document.addEventListener('DOMContentLoaded', function(){})
```

：仅当DOM加载完成时候触发，不包括样式表、图片等



### 调整窗口大小事件

window.addEventListener('resize', function(){}) 

应用场景：响应式布局（例如根据页面实际大小显示或隐藏部分元素）



### 计时器

```
window.setTimeout(callback, time)
```

其中callback为回调函数，time为毫秒为单位的时间，实际使用中常常将计时器赋值给一个变量来控制：

```
var timer = setTimeout(func1, 1000);
```

重复执行的计时器：setInterval

```
window.setInterval(func1, time)
```

使用方式同上，其特点在于**每隔 time ，都会执行函数**，可以用于实现倒计时效果，例如在注册页面发送短信验证码的时候，往往会设置间隔时间：

```js
var btnSend = document.querySelector('button');
btnSend.addEventListener('click', function(){
    var restTime = 6;
    btnSend.disabled = true;
    // btnSend.innerHTML = restTime + '秒后重新发送';
    var timer = setInterval(function(){      
        if(restTime == 0){
            btnSend.disabled = false;
            btnSend.innerHTML = '发送';
            clearInterval(timer);
        }else{ 
            btnSend.innerHTML = restTime + '秒后重新发送';
            restTime--;
        }
    }, 1000);  
})
```

 ### this指向

- 全局作用域以及普通函数的情况下，this 指向 window（相当于window调用）
- 哪个对象调用函数，函数内的 this 就指向哪个对象
- 构造函数内的 this 指向创建的对象



### 执行队列

JS是单线程语言，同一时间只能做一个操作，同步和异步：

- 同步：顺序执行程序，自上而下依次执行
- 异步：先执行直接执行的代码部分，对于需要等待的部分，后续执行

在代码执行的过程中，同步任务都在主线程上执行，形成一个执行栈，异步则是通过回调函数实现，异步任务主要有三种：

- 普通事件：click，resize
- 资源加载：load，error
- 定时器：setInterval，setTimeout等

代码执行 -> 同步任务执行 -> 异步任务放入任务队列中  -> 同步任务执行完毕 -> 依次执行任务队列中的任务

### location

URL：统一资源定位符，其一般格式为：

```
protocol://host[:post]/path/[?query]#fragment
```

- protocol: 通信协议
- host：主机、域名
- port：端口号
- query：参数

location的常用属性：

- location.href：获取URL
- location.host：获取主机名
- location.search: 返回参数
- location.hash：返回#后面内容，常见于链接、锚点

常用方法：

- location.assign(): 跳转页面，记录历史
- location.replace()：替换页面，不记录历史
- location.reload()：重新加载页面，相当于刷新页面，参数如果为 true 表示强制刷新 （ctrl + f5）



## 元素可视区 client 系列

动态得到元素边框以及大小

- element.clientTop: 上边框大小
- element.clientLeft: 左边框大小
- element.clientWidth： 返回 内容+padding 的大小，不包含边框，返回的结果不带单位
- element.clientHeight



## 立即执行函数

```js
(function(){
    console.log(1);
})();
```



## scroll

scroll 系列的属性可以动态得到元素的大小和滚动距离等等

- element.scrollTop(Left)：返回被卷去的上侧距离，返回的结果不带单位（也就是当前显示的区域距离文本最上沿的距离）
- element.scrollWidth(Height)：返回自身实际的高度，不含边框（clientX则是返回固定大小）



## 三种距离系列大小对比

- element.offsetWidth: 返回包含自身 padding、边框、内容区的宽度，返回结果不带单位
- element.clientWidth：返回自身包括 padding、内容的宽度，不含边框，返回的结果不带单位
- element.scrollWidth：返回自身实际的宽度，不含边框

应用场景:

- offset 系列常用于获得元素的位置
- client 系列则是获取元素的大小
- scroll 系列用于获取滚动距离
- 页面滚动距离通过 window.pageXOffset 获得



## mouseover 和 mouseenter

mouseover：经过子盒子也会触发事件（冒泡）

mouseenter：经过子盒子不会触发  -- mouseleave



## 计时器封装

例如：

```js
<div class="div1"></div>
<div class="div2"></div>
<script>
    var div1 = document.querySelector('.div1');
var div2 = document.querySelector('.div2');

// 将相同动画封装
function animate(obj, target){
    // 先清除所有的计时器，防止出现点击多次初始化计时器
    clearInterval(obj.timer);
    obj.timer = setInterval(() => {
        // console.log(obj.style.left);
        // 不可以使用 obj.style.left >= target 比较（带单位）
        if(obj.offsetLeft >= target){
            clearInterval(obj.timer);
        }
        obj.style.left = obj.offsetLeft + 1 + 'px';
    }, 500);
}

animate(div1, 300);
animate(div2, 400);
</script>
```

