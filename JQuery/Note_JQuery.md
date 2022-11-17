# jQuery

## 引入

- production： 压缩版
- development：开发使用

引入文件：

```js
<script src="jquery.min.js"></script>
```



## 入口函数

简写形式：

```js
$(function(){
    $('div').hide();
});
```

相当于原生js中的 DOMContentLoaded - DOM的结构加载完毕后就会执行，不必等待所有资源全部加载完成（js的load是等待所有资源全部加载完成才执行内部代码）



## 顶级对象 $

`$`  符号相当于 jQuery，对应原生js中的 window 对象

**注意区分DOM对象和jQuery对象：**

- 通过原生js获得的对象是 DOM 对象
- 使用 jQuery 语法得到的对象是 jQuery 对象 （对DOM对象进行包装） ，伪数组形式，可以使用 `$('xxx')[0]`的性质转化为 DOM对象进行操作

两者方法不能混合调用



## jQuery 对象和 DOM 对象的转化

由于原生 js DOM对象比 jQuery 对象的范围更大，因此在一些情况下需要将两者对象进行转化（例如jQuery不提供 video的play方法，需要先转化为 DOM 对象再进行处理）：

```js
var myvideo = document.querySelector('video');
// 转化为 jQuery 对象
$(myvideo)

// 转化为 DOM 对象 jQuery 本质上是得到伪数组 因此可以根据索引进行对象的获取
$('video')[0].play()
$('video').get(0).play()
```

## jQuery API

常见选择器：

```
$('#id')  - ID 选择器
$('*')  - 全选选择器
$('.class')  - 类选择器
$('div')  - 标签选择器
$('div, li')  - 并集选择器
$('div.li')  - 交集选择器

$('ul li') - 后代选择器 （所有后代）
$('ul>li') - 子代选择器 （亲儿子层级）
```

所得到的结果均为伪数组形式，注意两种后代的选择方式的应用 `ul li` , `ul>li`

## 隐式迭代

自动遍历内部的DOM对象，并执行相应的方法，例如对页面中多个 div 进行改变，使用jQuery 获得所有对象中，直接修改样式即可

```js
<ol>
    <li>1</li>
<li>2</li>
<li>3</li>
<li>4</li>
</ol>

<script>
    $(function(){
    $('li').css('backgroundColor', 'lightgreen');
})  
</script>
```



## 筛选选择器

常用筛选选择器：

```
$('li:first') - 首元素
$('li:last')  - 末尾元素
$('li:eq(2)') - 索引号为2的元素 索引号从0开始
$('li:odd')  - 奇数索引元素
$('li:even') - 偶数索引元素
```

例如：

```js
$('div:eq(2)').css('background', '#ebebeb');
$('div:even').css('background', 'lightgreen');
```

## 筛选方法

```
$('li').parent()  查找最近父级元素

$('ul').children('li') == $('ul>li') 查找亲儿子

$('ul').find('li') == $('ul li') 查找所有后代

$('.first').siblings('li') 查找兄弟节点（不包括自己）用于设置除自身外的样式

$('.first').nextAll()
$('.first').prevAll() 查找当前节点之前（之后）的所有同级元素

$('div').hasClass('protected') 检查当前的元素是否含有某个特定的类 （boolean）

$('li').eq(3) == $('li:eq(3)') 选择对应索引的元素 更推荐使用方法 直接使用index即可使用
```

其中使用最多的是 parent children find siblings 和 eq 方法

`$('e').siblings('e')`配合隐式迭代可快速实现排他设计

获得 li 标签的索引： `$(this).index()`

可以使用链式编程同时对多个元素进行处理：

```js
$('div').show().siblings().hide()
```

##  样式修改

- 简单修改 ： 直接通过 css 修改

  - 参数只写属性名 返回的是属性值 `$(this).css('color')`（返回结果带单位）

  - 修改：`$(this).css('height', 10)`(数值可以带引号或不带)

  - 通过对象方式修改：

    ```js
    $('div').css({
        // 数值可以不加引号
    	width: '400px',
        height: 400,
        // 复合属性必须符合小驼峰命名
        backgroundColor: '#ccc',
    })
    ```

    

- 复杂修改：通过类名修改

  - 增加类 ： `$('div').addClass('class')`
  - 删除类：removeClass()
  - 切换类：toggleClass() : 如果有这个类就移除，反之则加上



## 元素显示与隐藏

显示： 

```
.show([speed], [easing], [function])
分别表示 速度 (normal, fast, slow, 1000)
         切换效果 (swing, linear)
         回调函数
```

隐藏： `.hide()`，同样有上述参数

切换： `.toggle()` ，显示 与 隐藏 两个状态之间切换



## 滑动与切换

```js
$('#slideDown').on('click', function(){
    $('.test2').slideDown();
})

$('#slideUp').on('click', function(){
    $('.test2').slideUp();
})

$('#slideToggle').on('click', function(){
    $('.test2').slideToggle();
})
```

应用场景：tab 切换，鼠标经过时显示下拉菜单，离开时隐藏下拉菜单

方案一：直接设置元素的隐藏与显示

```js
// tab 切换： 直接显示隐藏
$('.tab>li').on('mouseover', function(){
    $(this).children('ul').show();
})

$('.tab>li').on('mouseout', function(){
    $(this).children('ul').hide();
})
```



方案二：增加平滑的动画效果

```js
// 滑动效果
 $('.tab>li').on('mouseover', function () {
     // 注意使用 stop() 将尚未执行完的动画效果停止
     $(this).children('ul').stop().slideDown();
 })

 $('.tab>li').on('mouseout', function () {
     $(this).children('ul').stop().slideUp();
 })
```

方案三：使用事件切换简化

```js
 // 事件切换
 hover(function1, function2) func1表示鼠标经过 func2鼠标离开
 $('.tab>li').hover(function(){
     $(this).children('ul').stop().slideDown();
 }, function(){
     $(this).children('ul').stop().slideUp();
 })
```

如果 hover 只设置一个函数，则表示鼠标移入移出都触发该事件：

```js
// 事件切换 -- 如果 hover(func1) 只写一个函数 则鼠标经过、离开都会触发
$('.tab>li').hover(function(){
    $(this).children('ul').stop().slideToggle();
})
```



## 淡入淡出效果

淡入效果：

```
fadeIn([speed], [easing], [fn])
```

淡出效果为 `fadeOut`，参数同上

修改透明度：

```
fadeTo([speed], opacity, [easing], [fn])
```

其中 opacity 是必要参数，取值为 0-1 之间，speed 也是必要参数，可取值为 （slow， normal， fast）或者具体毫秒数

应用场景：多个子元素，当前选中的高亮显示，其他元素降低透明度：

```js
$('.content>div').hover(function(){
    $(this).siblings().stop().fadeTo(500, 0.5);
}, function(){
    $(this).siblings().stop().fadeTo(500, 1);
})
```

当鼠标选中当前元素的时候，设置其他元素的透明度为0.5， 通过降低其他元素的透明度高亮显示当前元素

**注意对于鼠标移入移出都要设置效果的场景，使用 `hover()`函数同时设置两个效果函数可以简化代码**

## animate

创建动画效果，格式：

```
animate(params, [speed], [easing], [fn])
```

其中 params 以对象方式传递，属性名可以不使用引号，但如果是复合属性，必须要采用驼峰命名法



## 属性操作

### 获取与设置元素的固有属性

固有属性是指元素自身带有属性，获取方法：

```
element.prop('属性名')
```

属性设置：

```
element.prop('属性名', '属性值')
```



### 自定义属性操作

```
element.attr('属性名')

element.attr('属性名', '属性值')
```



### 数据缓存 data()

data()方法可以在指定元素上存取数据，但是不会修改 DOM 结构，一旦页面刷新，之前存放的数据都会消失

```
element.data('属性名', '属性值')
```

此时元素实际的DOM不会改变，属性名、属性值只是存在于内存中

## 内容文本值

- 内容,包含标签（inner HTML（））：

  ```
  element.html()
   // 修改
   element.html('修改后的内容')
  ```

- 文本（innerText（））：

  ```
  element.text()
  ```

- 表单值（value（））：

  ```
  input.val() 
  ```



## 元素操作

### 遍历元素

```
$('div').each(function(index, domEle){....})
```

注意：

- index 可以使用任意字符代替，表示当前元素的索引
- domEle表示获得的当前元素，**domEle是DOM对象**，所以如果使用 jq 的方式对其进行操作，需要先使用 `$(domEle)` 转化为 jq 对象

应用场景：对每个元素设置不同的颜色 - 将颜色保存为数组，按照索引逐个设置

```js
var colorArr = ['lightgreen', 'lightblue', 'lightgrey', 'skyblue'];
$('li').each(function(index, domEle){
    $(domEle).css('backgroundColor', colorArr[index]);
});
```

`$('E').each` 方法主要用于遍历DOM，第二种遍历方法 `$.each(object)`则主要适用于遍历对象和处理数据：

```
$.each(object, function(index, element){...})
```

例如：

```js
$.each(colorArr, function(i, ele){
    console.log(i+' -- '+ele); 
})

$.each({name: 'zs', age: 12, gender:'man'}, function(i, ele){
    console.log(i + ' -- ' + ele);
})
```



### 添加元素

内部元素的添加方式 （向 ul 中添加 li）：

```js
var li = $('<li>new one</li>');  // 创建元素
$('ul').append(li);   // 在最后添加
$('ul').prepend(li);  // 在开头添加
```

外部元素的添加方式：

```js
// 外部元素的添加方式
var newDiv = $('<div>new div </div>');
$('div').after(newDiv);
$('div').before(newDiv);
```



### 删除元素

三种方式删除元素：

- 删除自身元素：

  ```js
  $('ele').remove()
  ```

- 删除子元素（清空孩子）：

  ```js
  $('ul').empty()  // 删除 li 元素
  ```

- 删除子元素：

  ```js
  $('ul').html("")  // 清空内容
  ```



## 尺寸、位置操作

### 尺寸

获取尺寸：

```js
// 元素实际大小的尺寸
console.log($('.test').width());
console.log($('.test').height());
// 元素实际大小 + padding大小
console.log($('.test').innerWidth());
console.log($('.test').innerHeight());
// 元素实际大小 + padding + border
console.log($('.test').outerHeight());
console.log($('.test').outerWidth());
// 元素实际大小 + padding + border + margin
console.log($('.test').outerHeight(true));
console.log($('.test').outerWidth(true));
```

上述方法如果不加参数，表示返回响应的值，如果**加数字参数则表示设置相应的数值**，数值可以不带单位

### 位置

获取元素相对于文档的位置，使用`ele.offset()`，其返回的结果为对象形式，使用具体的 left 或 top 值可以使用`ele.offset().top`，注意即使设置了定位，该方法得到的结果依然是相对于文档的位置。修改位置方法：

```js
$('ele').offset({
	top: 200,
	left: 300
})
```

使用 `ele.postion()`返回元素相较于父元素的位置，其获取位置属性同上，**注意该方法不能设置属性值**

对于页面滚动的元素，使用`ele.scrollTop()`得到元素距离文档顶部的距离，该方法用于设置 **回到顶部** 效果（距离文档顶部的距离超过一定距离后显示 “回到顶部” 的按钮）



## 事件

### 事件注册

单个事件注册： click， mouseover，mouseout，blur，change，keydown，keyup，resize，scroll，例如：

```
$('div').click(function(){...})
```

使用 `ele.on()`可以同时注册多个事件， 例如：

```js
// on 接收一个对象 在对象中定义触发事件以及效果
$('.test1').on({
    click: function(){
        $(this).css('background', 'lightgreen');
    },
    mouseenter: function(){
        $(this).css('width', 150);
    },
    mouseleave: function(){
        $(this).css('width', 100);
    } 
});
```

on 同样可以为多个触发事件定义同一个效果函数，例如：

```js
// 多个触发事件 以空格分隔
$('.test1').on('mouseenter mouseleave', function(){
	$(this).toggleClass('current');
})
```

利用 on 方法的第二个参数，可以实现委派：

```
$('ul').on('click', 'li', function(){...})
```

例如，为 ul 中的每个 li 绑定点击事件，可以为 ul 绑定事件，然后传入子选择器参数，这样触发的对象实际上是子元素：

```js
$('ul').on('click', 'li', function(){
    console.log($(this).text());
});
```

使用 on 最重要的特点是可以为动态创建的元素绑定事件，例如，对于页面中的 ol 元素，通过 js 动态插入一个 li 元素，此时使用 click 是无法绑定事件的（未来发生的事件），这种情况可以使用 on 绑定事件。

### 事件解绑

解绑某个元素的全部事件：

```
$('div').off()
```

解绑某个特定事件：

```
$('div').off('click')
```

解绑事件委托（添加子元素参数）：

```
$('ul').off('click', 'li')
```

另外， 如果只想让某个事件触发一次，可以使用 `one()`方法：

```js
$('.test2').one('click', function(){
    alert('one time.');
})
```



### 自动触发事件

直接调用事件：

```
$('div').click()
```

使用 trigger 函数：

```
$('div').trigger('focus')
```

使用 triggerHandler 函数（不会触发元素的默认行为，例如对 input 设置 focus 事件的时候不会使光标闪烁）

```
$('div').triggerHandler()
```



### 事件对象

事件触发后就会有事件对象产生，可以利用事件对象阻止默认行为或阻止冒泡：

```
event.preventDefault()
event.stopPropagation()
```



### 对象拷贝

方法：

```
$.extend([deep=false/true], targetobj, obj)
```

将 obj 对象中的属性复制到 targetobj 中，deep 属性默认为 false 表示浅拷贝

浅拷贝方式：

- 简单数据类型数据直接复制
- 复杂数据类型的数据直接复制数据地址，因此 targetobj 或 obj 的属性改变都会影响两个
- 相同属性shi

深拷贝方式：

- 简单数据类型一致
- 复杂数据类型会重新复制一份，因此之后的修改不会影响原来的对象，如果复杂数据中存在其他属性的数据，会在结果中添加而不是覆盖



## 多库共存

jq 可能会与其他的 JS 库产生冲突情况，jq 有两个解决方案：

- 直接使用 jQuery 代替 $ 符号

- 为 jq 自定义新的名称： 

  ```
  var xxx = $.noConflict()
  ```

   