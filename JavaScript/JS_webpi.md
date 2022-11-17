# Web APIs

## 区别

JavaScript：

- ECMAScript  - 基础
- Web APIs
  - DOM 页面文档模型  
  - BOM  浏览器文档模型

## Web APIs

Web API主要是针对浏览器提供的接口，主要针对浏览器做交互效果，一般都有输入和输出，其很多都是方法

## DOM

文档对象模型，用于处理HTML、XML

### DOM 树

- 文档：整个页面就是一个文档，DOM中使用 document 表示
- 元素：页面中所有的标签都是元素，DOM中使用 element 表示
- 节点：页面中所有的元素都是节点，DOM中使用 node 表示

**DOM中将以上内容都看作对象**

整个文档可以用一棵树的形式表示：

```
文档 - <html>根元素 - <head>元素 - <title>元素
                   - <body>元素 - <a>元素
                                - <href>元素
```

### 获取内容

获取元素内容的方法：

- 通过元素ID：

  ```javascript
  <div id="time">
      2022-7-19
  </div>
  // 页面从上往下加载 所以将 js 脚本放在元素后面
  <script>
      var time = document.getElementById('time');
  // 返回的结果为 object
  console.log(typeof time);
  console.log(time);
  // 打印对象详细内容
  console.dir(time);
  </script>
  ```

  

- 根据标签：

  ```javascript
  // 返回的结果是伪数组
  var lis = document.getElementsByTagName('li');
  console.log(lis);
  console.dir(lis);
  // 可以通过遍历数组的形式访问每个对象
  for(var i=0; i<lis.length; i++){
      console.log(lis[i]);
  }
  
  // 可以通过父元素进行访问 注意父元素必须是单个元素
  var ul = document.getElementById('ul');
  var lis1 = ul.getElementsByTagName('li');
  console.log(lis1);
  ```

  

- H5新增的选择方法：

  - 根据类名获得元素  var boxs = document.getElementByClassName('box') ，注意这里只写类名即可，无需`.`
  - querySelector() 返回指定选择器的**第一个对象**，由于需要区分，所以**要加符号**
    - var firstBox = document.querySelector('.box')  返回整个文档中查询到的第一个 box 元素
    - var nav = document.querySelector('#nav') 由于 id 一般是唯一的 所以一般直接返回的就是此 id 的元素
    - var li = document.querySelector('li') 选择的是所有的 li 元素中的第一个
  - querySelectorAll() : 返回的是所有可选择对象的集合，书写规范同上

获取 body 和 html 对象：

- var bodyEle = document.body
- var htmlEle = document.documentElement



## 事件

网页中每个元素都可以产生某些可以触发JS的事件，事件是指可以被JS侦测到的行为，即：触发 -- 响应机制

组成（事件三要素）：

- 事件源  --- 事件被触发的对象
- 绑定事件类型  --- 如何触发，例如鼠标单击等等 （btn.onclick = function(){  }）
- 事件处理程序  --- 通过一个函数赋值的方式完成

常用的鼠标事件：

- onclick ： 鼠标单击左键
- onmouseover：鼠标经过
- onmouseout：鼠标离开
- onfocus：获得鼠标焦点
- onblur：失去鼠标焦点
- onmousemove：鼠标移动触发
- onmouseup：鼠标弹起
- onmousedown：鼠标按下

## 操作元素

利用DOM操作元素来改变元素的内容、属性等

- **element.innerHTML**: 起始位置到终止位置的全部内容 包括 html 标签，同时保留空格和换行（**使用更多**）

  - 识别html标签，可以对元素内部进行修改

  - 可进行读写，读出内容的时候也会显示标签和空格换行

    ```javascript
    var divTime = document.querySelector('div');
    var innerHtml = divTime.innerHTML;  //读内容
    console.log(innerHtml);
    ```

    

- element.innerText： 不包括 html 标签，同时换掉空格和换行（非标准）

  - 不识别html标签，直接对html标签进行显示

  - 可进行读写，读出内容的时候忽略标签，空格和换行

    ```javascript
    var divTime = document.querySelector('div');
    var innerHtml = divTIme.innerText;   // 读内容
    console.log(innerHtml)  // 此时输出的结果会去掉所有的html标签，空格和换行
    ```



案例：修改图片的信息

```javascript
var btnImg1 = document.querySelector('#btnImg1');
var btnImg2 = document.querySelector('#btnImg2');
var showImg = document.querySelector('img');
btnImg1.onclick = function(){
    showImg.src = 'images/img1.png';
    showImg.title = 'pic1';
}
btnImg2.onclick = function(){
    showImg.src = 'images/img2.png';
    showImg.title = 'pic2';
}
```

同样可以修改的属性有 href，id，alt，title 等等

### 表单修改

利用DOM可以对表单元素的 type，value，checked，selected，disabled等属性进行改变：

```javascript
var btnChange = document.querySelector('#btnChange');
var showText = document.querySelector('#showText');

btnChange.onclick = function(){
    showText.value = 'change the showing text.';
    btnChange.disabled = true;
    // this 代指的是调用者
    this.disabled = true;
}
```

应用场景：密码输入框可以点击显示密码

- 点击之后将input的类型，由password变化为text
- 由于再次点击按钮后，需要再次转化为密码类型，因此可以定义一个flag变量，如果是状态一就转化为密码类型，如果是状态二就转化为明文类型
- 用于显示密码的图标往往是一个小眼睛（有睁开和闭上两种状态），这里可以使用一个 label 标签，包裹一个 img 标签，img 中展示的就是睁开/闭上的图片，label 标签也可以绑定 onclick 事件

核心：

```javascript
btnShowPwd.onclick = function(){
    if(flag==0){
        pwd.type = 'text';
        flag = 1;
    }else{
        pwd.type = 'password';
        flag = 0;
    }
}
```



### 修改样式

通过JS对元素样式进行修改：

- element.style:	行内样式改变
- element.className:  类名样式操作

注意：

- JS的样式均使用小驼峰命名法 ： fontSize ， backgroundColor等
- 通过JS对样式进行修改，产生的是行内样式，css权重较高（可以检查执行后的代码，改变的样式作为行内样式添加进元素中）

例如：

```javascript
div.onclick = function () {
    div.style.backgroundColor = 'lightgreen';
    div.style.width = '200px';
}
```

设置元素隐藏：网页悬浮的小对话框等，点击后隐藏

```
div.style.display = 'none'
```

显示 × 的元素可以通过一个`<i>`标签制作，然后设置其样式`cursor:pointer`，最后通过 JS 设置点击事件

input 元素失去焦点与获得焦点的样式改变：

- element.onfocus = function(){xxx}
- element.onblur = function(){xxx}

### 通过类名修改元素

如果修改的元素样式较少，可以直接通过 element.style.xxxx 修改，如果要修改的样式较多，可以直接将结果定义为一个为类：

```javascript
// 要修改的样式较多 直接写成类的形式
// this.className = 'change';

// 上述修改方法会覆盖掉原始的类名
// this.className = 'first change';
var originClass = this.className;
// 注意空格
this.className = originClass + ' change';
```



### 排他思想

如果有一组元素，想要对其中的某个元素进行操作，使用循环的方法是：

- 将所有的元素样式清除
- 设置某个特定元素的样式

应用场景：轮播图下面的小选择圆点



### 鼠标经过事件

使用：

- onmouseOver
- onmouseLeave

应用场景：表格，鼠标移动上去时改变颜色

**注意**：类似这种样式改变比较彻底，比较多的情况，简单的做法是将改变后的样式归纳为一个类，触发事件的时候直接改变类名，注意通过 element.className = 'class' 的方式会覆盖掉元素原始的类，可以先获取元素原来的类名， 然后与新的类名进行拼接即可



### 自定义属性操作

```javascript
// 直接 element.properity 输出属性
console.log(div.className);
console.log(div.id);

// 使用 element.getAttribute()
console.log(div.getAttribute('class'));
console.log(div.getAttribute('id'));


// 使用 element.setAttribute(a, b)
div.setAttribute('class', 'test2');
div.setAttribute('id', 'test_id2');

// 使用 removeAttribute 移除属性
div.removeAttribute('id');
```

对于元素原本存在的属性，可以直接使用 element.properity 读写，对于自定义的属性，则使用 set/getAttribute 读写

为了方便区别自定义属性与元素自带的属性，H5规定所有的自定义属性以data开头，例如:

```
<div data-index="1"></div>
```

**应用场景：tab栏切换**

实现 tab 栏切换的步骤：

1. 排他思想1：对所有的选项卡（一般使用 li 实现）设置样式为未选中
2. 对点击的选项卡设置样式为选中
3. 获取选择到的选项卡的索引号（注意这里不能在使用循环绑定时候自定义的索引号）
4. 排他思想2：对所有的div设置display none
5. 对指定序号的div设置显示



### 节点操作

文档中的所有内容都是节点，节点至少拥有 nodeType、nodeName 以及 nodeValue 三个基本属性

- 元素节点：nodeType 为 1 （主要操作对象）
- 属性节点：nodeType 为 2
- 文本节点（文字、空格、换行等）：nodeType 为 3

#### 获取元素的父节点/子节点

获取元素最近的父节点

```
var parentNode = child.parentNode
```

获取子节点有两种方式：

- 标准方式：

  ```
  var childs = ele.childNodes;
  ```

  这种方式会返回元素的所有子节点，包含元素节点和文本节点，如果要操作元素节点，需要根据 nodeType 进行判断

- 非标准方式：

  ```
  var childs = ele.children;
  ```

  只返回元素节点

特殊位置的子节点：

- 第一个或最后一个子节点，使用`ele.children[0]`，`ele.children[len-1]`获得

**应用场景：鼠标指向的时候显示下拉菜单**，其实现过程为：

1. 创建一个 ul，每个子元素 li 包含两部分：
   1. 鼠标移动的区域，使用 a 标签实现
   2. 鼠标移动后显示的区域，这部分是一个列表
2. 为 li 设置浮动，同时由于未显示区域需要使用绝对定位，因此要设置每个li为相对定位
3. 将1.2鼠标移动后才展示的菜单（ul）设置 display 为 none
4. 为每个鼠标显示区域 li （也可以是a）添加 onmouseover 事件

这个过程中，对于每个第一层 li 元素 其children 都有两个元素，第一个为 a ，第二个为 ul 列表，可以通过循环遍历外层 li，为其 children[0] （也可以直接为 li 绑定）绑定 onmouseover 事件，显示 children[1] 即可。

另一个注意点是在实现列表类的样式时，往往会出现 li 元素包含 a 元素的情况，此时对于文字颜色等等属性进行设置的时候，可以对 li 设置，也可以对 a 设置，但是要考虑到离文本最近的标签是a，因此这类属性设置在 a 的样式中会更合适

#### 创建、添加节点

- 创建节点：var node = document.createElement('li')

- 添加节点：

  - 追加方式：

    ```
    var ul = document.querySelector('ul');
    ul.append(node);
    ul.appendNode(node);
    ```
  ```
  
  ```
  
- 插入：
  
    ```
    var ul = document.querySelector('ul');
    ul.insertBefore(node, ul.children[0])
  ```
  
    

**应用场景：留言板**，信息发表之后，在页面中插入该信息

#### 两种添加元素的效率比较

- element.innerHTML 
  - 直接单次写入方式由于使用的是字符串拼接，因此效率最低
  - 先使用数组方式存储内容，然后一次性写入方式效率最高
- document.createElement() ： 效率稍低于使用数组方式写入innerHTML的方式，但结构清晰

### DOM 主要内容

- 创建元素
  - innerHTML
  - document.createElement
- 增加元素
  - appendChild
  - insertBefore
- 删除元素
  - removeChild
- 修改元素属性
  - 直接修改元素原有属性
    - src 、href、 title等
  - 修改普通元素内容
    - innerHTML、innerText
  - 修改表单属性
    - value、type、disable
  - 修改样式
    - style、className
  - 查找元素
    - DOM api getElementByID等方式已经不推荐使用
    - H5新增 querySelector, querySelectorAll
    - 利用节点操作 parentNode, children, previousElementSibling, nextElementSibling
  - 自定义属性操作
    - setAttribute 设置
    - getAttributs 获取
    - removeAttribute 移除
  - 注册事件
    - onclick
    - onmouseover、onmouseout
    - onfocus、onblur
    - onmousemove
    - onmouseup、onmousedown











## 其他

### 注意 == 与 ===

== 在比较之前会尝试进行类型转换， === 则是严格要求类型也一致，例如：

```javascript
var v1 = '1';
var v2 = 1;
if(v1 == v2){
    console.log('== equal');
}else{
    console.log('== not equal');
}

if(v1 === v2){
    console.log('equal');
}else{
    console.log('=== not equal');
}
```



