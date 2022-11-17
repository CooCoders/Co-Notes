## HTML 与 CSS 进阶

### H5 新增特性

注意新特性都有兼容性问题



#### 1. 新增的语义化标签

- `<header>`：头部标签
- `<nav>`：导航标签
- `<article>`：内容标签
- `<section>`：定义文档的某个区域（与div类似）
- `<aside>`：侧边栏标签
- `<footer>`：尾部标签

注意：

- 这些语义化标准主要是针对搜索引擎的的
- 每个标签可以使用多次
- 在 IE9 中，需要把这些元素转化为块级元素
- 在移动端不存在兼容性问题，因此更适合这些标签



#### 2. 视频标签

标签：`<video>`，不同浏览器支持的视频文件格式不同，尽量使用`.mp4`

语法：

```html
<video>
    <source src="x.mp4" type="video/mp4">
    <source src="x.ogg" type="video/ogg">
</video>
```

常用属性：

- autoplay ： （autoplay='autoplay'）视频就绪自动播放（chrome浏览器需要添加 muted 属性）
- controls：（controls="controls"）显示播放控件（一般用 JS 自行设置，不同浏览器有不同样式）
- loop：（loop=”loop“）设置视频循环播放
- preload：（preload=”auto/none“）规定是否预加载视频（如果设置了 autoplay 属性，则忽略）
- poster：（poster="imgurl"）加载视频时候显示的图片
- muted：（muted=”muted“）设置静音播放



#### 3. 音频标签

标签: `<audio>`

常见属性：

- autoplay： 设置音频自动播放 （Chrome浏览器不支持音频文件自动播放，可以通过 JS 控制）
- controls：显示播放控件
- loop：循环播放
- src：url



#### 4. 新增的input表单

用于特定场景的 input 标签，注意这些标签必须使用于 form 表单内，在 submit 时进行验证，例如：

```html
<form action="#">
    <ul>
        <li>e-mail: <input type="email"></li>
        <li>websit: <input type="url"></li>
        <li>data: <input type="date"></li>
        <li>time: <input type="time"></li>
        <li>number: <input type="number"></li>
        <li>phone: <input type="tel"></li>
        <li>search: <input type="search"></li>
        <li>color: <input type="color"></li>
        <li><input type="submit" value="sub"></li>
    </ul>
</form>
```

其中使用最多的是 `number`, `tel`和`search`



#### 新增表单属性

常见表单属性：

- required=“required” ： 表单拥有该属性表示其内容不能为空，必填
- placeholder=“text”： 表单提示信息，默认文本
- autofocus=“autofocus”：自动聚焦属性，当页面加载完成时自动聚焦到指定表单
- autocomplete=“on/off”： 当用户在字段开始输入信息时，浏览器保留之前信息，默认值为'on'，关闭使用"off"属性（使用条件，需要放在表单内，并且设置name属性，同时成功提交）
- multiple="multiple"：适用于 file 表单，可以多选文件提交
- 注意：**上面的属性都要在 form 内使用**

另外，placeholder文本可以进行控制：

```css
input::placeholder{
  color: blue;
}
```



### CSS3 的新特性

#### 1. 属性选择器

使用属性选择器可以更精确的选择某元素

1. `E[att]`：选择具有 att 属性的元素
2. `E[att="val"]`：选择具有 att 属性并且属性值等于 val 的元素
3. `E[att^="val"]` ： 选择具有 att 属性并且属性值以 val 开头的元素
4. `E[att$="val"]` ： 选择具有 att 属性并且属性值以 val 结尾的元素
5. `E[att*="val"]` ： 选择具有 att 属性并且属性值中含有 val 的元素

注意：**类选择器，属性选择器，伪类选择器都具有一样的权重 10**



#### 2.结构伪类选择器

结构伪类选择器主要根据文档结构选择元素，常用于根据父级选择器里面的子元素

1. `E :first-child`：匹配父元素的第一个子元素 （有空格）
2. `E :last-child`：匹配父元素中最后一个子元素
3. `E :nth-child(n)`：匹配父元素中的第n个子元素
4. `E :first-of-type`：指定类型的第一个元素
5. `E :last-of-type`：指定类型的最后一个元素
6. `E :nth-of-type(n)`：指定类型的第n个元素

一般写的更具体：

```
ul li:first-child{   //前为后代选择器， 后面是伪类选择器 表示ul的后代li中第一个元素
  xxx
}
```

对于 `nth-child(n)`，其中 n 可以是数字、关键字或者公式

- 数字：表示第几个
- 关键字：even 选择偶数元素，odd选择奇数元素 （实现隔行变色效果）
- **公式：使用自变量为 n 的公式（从0开始计数，逐次累加，如果n=0则忽略掉）：**
  - 2n：选择所有偶数
  - 2n+1：选择所有的奇数
  - 3n：选择所有3的倍数
  - n+5：从第5个开始选择
  - -n+5：选择前5个元素

`E :nth-of-type(n)`的使用：主要是针对特定类型的元素：

```html
<div>
    <p>pre</p>
    <div>11</div>
    <div>22</div>
    <div>33</div>
    <div>44</div>
</div>
```

如果只针对 div 元素进行处理：

```css
div div:nth-of-type(-n+2) {
	background-color: lightgreen;
}
```

这样会忽略第一个 p 元素。

总结：

- nth-child：对父元素的所有孩子都排序，找到第n个孩子即可
- nth-of-type：对父元素中指定子元素进行排序，然后再去寻找第n个孩子



#### 3. 伪元素选择器

伪元素选择器可以帮助我们利用CSS创建新建标签元素，而不需要HTML标签，简化HTML结构

- `::before`：在元素内部的前面插入内容
- `::after`：在元素内部后面插入内容

注意：

- before 和 after 创建一个元素，为**行内元素**（所以直接设置宽度高度是无效的，要注意设置转换块级或行内块元素）
- 新创建的这个元素在文档树内是找不到的，所以被称为伪元素
- before 和 after 必须有 content 属性
- 伪元素选择器和标签选择器一样，权重为1



案例：

```css
// 声明字体
@font-face {
    font-family: 'icomoon';
    src: url('fonts/icomoon.eot?dgvgwb');
    src: url('fonts/icomoon.eot?dgvgwb#iefix') format('embedded-opentype'),
        url('fonts/icomoon.ttf?dgvgwb') format('truetype'),
        url('fonts/icomoon.woff?dgvgwb') format('woff'),
        url('fonts/icomoon.svg?dgvgwb#icomoon') format('svg');
    font-weight: normal;
    font-style: normal;
    font-display: block;
}

div {
    position: relative;
    height: 50px;
    width: 200px;
    border: 1px solid black;
}
//使用伪元素实现小箭头符号
div::after {
    position: absolute;
    top: 16px;
    right: 20px;
    font-family: 'icomoon';
    content: '\ea3c';
}
```

这样html主体中只需要定义一个 div 元素即可：

```
<div></div>
```



伪元素选择器应用：清除浮动

清除浮动的原因：

1. 父盒子没有高度（靠子盒子高度撑开）
2. 子盒子设置浮动
3. 对父盒子下面的布局产生影响



```css
.clearfixx::after {
    content: "";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
}
```

双伪元素清除浮动：

```css
.clearfix::before,
.clearfix::after {
    content: "";
    display: table;
}

.clearfix::after {
    clear: both;
}
```



#### 4.CSS3 盒子模型

通过 `box-sizing`属性指定盒子模型，两种盒子模型计算盒子大小的方式不同：

1. box-sizing: content-box 盒子实际大小为 content + padding + border（默认）
2. box-sizing: border-box 盒子实际大小为 width

因此预设值的样式可以设置为：

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
```



#### 5.CSS3 其他特性

滤镜： filter + 函数

```
img{
  filter: blur(5px);  //模糊化处理
}
```

calc 计算函数：在设置CSS属性的时候进行一些运算：

```
width: calc(100%-80px);
```



#### 6.过渡

transition ，添加过渡动画，从一个样式变换为另一个样式，经常搭配 `:hover`使用

```
transition: 过渡的属性 花费时间 运动曲线 何时开始（延迟时间）
```

- 过渡属性：想要变化的css样式属性，如果想要所有的属性都进行变化过渡，直接写一个 all 即可
- 花费时间：单位是 s（必须写单位）
- 运动曲线：（可省略）
- 何时开始：单位为 s（可省略），默认为 0s

多个属性变化：

```
transition: width 1s, height 1s;
```

进度条：

```html
<style>
    .bar {
    height: 10px;
    width: 150px;
    border: 1px solid red;
    border-radius: 3px;
    }

    .bar_in {
    height: 100%;
    width: 50%;
    background-color: red;
    transition: width 1s;
    }

    .bar:hover .bar_in {
    width: 100%;
    }
</style>


<div class="bar">
  <div class="bar_in"></div>
</div>
```









