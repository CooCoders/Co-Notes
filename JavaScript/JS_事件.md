# 事件高级

## 注册事件

两种注册事件的方式：

- 传统方式 onclick 等等
- 方法监听注册方式

### 传统注册方式

- 使用 on 开头
- **注册事件唯一，如果注册多个函数，只有最后一个能执行**

### 方法监听注册方式

- w3c推荐方式（IE9之后）
- addEventListener()
- 按照注册顺序依次执行，可以执行多个方法

## addEventListener 事件监听

格式：

```
eventTarget.addEventListener(type, listener, useCapture)

//eg
var btn = document.querySelector('button');
btn.addEventListener(a)
```

参数：

- type：事件类型，例如 click、mouseover等
- listener：事件处理函数
- useCapture：可选参数，布尔类型，默认为false, 设置为 true， 则处于捕获阶段

## 删除事件（解绑事件）

eg：

```javascript
var div = document.querySelector('div');
// 使用额外定义的function
div.addEventListener('click', func);
function func(){
    console.log('hello.');
    // 删除监听函数
    div.removeEventListener('click', func);
}
```



## DOM 事件流

事件执行有传播的过程，分为捕获和冒泡两种：

- 捕获：document -> html -> body -> div ...
- 冒泡：div -> body -> html ->document 

JS包含两个过程，但是执行代码的时候只能执行一个阶段，addEventListener的第三个参数，默认为 false ，执行冒泡过程，如果设置为 true 则执行捕获过程

**注意：**

- 实际开发常用冒泡
- 有些时间没有冒泡过程：onblur、onfocus、onmouseenter、onmouseleave



## 事件对象

```
eventTarget.onclick = function(event){}
eventTarget.addEventListener('click', function(event){})
```

其中，事件函数的形参 event 代表事件的状态，比如键盘按键的状态，鼠标位置等与此事件相关的一系列信息数据的集合都放到了这个对象里面。

注册事件的时候，系统会自动创建 event 对象，并依次传递给事件监听器

### target 属性

target 属性返回触发事件的对象，区别 target 和 this：

- e.target 返回的是触发的对象（如果对ul添加绑定，由于点击的时候是点 li，因此返回的是li）
- this 返回的是绑定事件的对象（对ul绑定点击，则返回的就是ul）

### type 属性

返回事件类型，例如 click

### preventDefault()方法

阻止默认事件，例如按钮的提交，链接的跳转

### 阻止冒泡 e.propagation()

```js
divSon.addEventListener('click', function(e){
    alert('son');
    e.stopPropagation();
},false);
```

添加 e.stoppropagation() 方法阻止冒泡传播，注意只能阻止当前元素的冒泡传播， 因此如果有其他的元素也存在冒泡，也需要加 stoppropagation()

### 事件委托

原理：不为每个子节点单独设置事件监听器，将事件监听器设置在父节点上，点击每个子节点的时候，由于冒泡原理，点击事件都会传递到父节点上。例如，对 ul 的每个子结点 li 注册点击事件的时候，可以对 ul 设置点击事件，点击 li 的时候，事件会冒泡到 ul 上，从而触发事件监听器。

### 阻止文本赋值

阻止对文本进行复制等操作 - 禁用右键菜单

```js
document.addEventListener('contextmenu', function(e){
    e.preventDefault();
})
```

禁止选中：

```js
document.addEventListener('selectstart', function(e){
    e.preventDefault();
})
```



## 鼠标事件对象

常用的事件对象有 MouseEvent 和 KeyboardEvent，常用的鼠标事件对象有：

**坐标：**

- e.clientX  e.clientY  ： 返回鼠标相对于可视区的坐标
- e.pageX  e.pageY ：返回鼠标相对于文档的坐标（height较大，页面出现滚动条）
- e.screenX  e.screenY：返回鼠标相对于电脑屏幕的坐标

**鼠标移动**

```
document.addEventListener('mousemove', function(e){})
```



## 键盘事件

- onkeyup
- onkeydown 按下时触发，支持全部的按键 
- onkeypress  按下时触发，不支持功能键

三个事件执行顺序不同，down - press - up

使用键盘监听事件的时候，可以输出各种属性：

- keycode（注意keyup 和keydown 不区别大小写，keypress区分）





