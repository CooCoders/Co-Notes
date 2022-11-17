### Note

#### Emmet语法

使用缩写，提高 html/css 的编写速度，vscode 已经集成

- 快速生成 HTML 结构语法
- 快速生成 CSS 样式语法

##### 1.快速生成 HTML 结构语法

- 生成标签：**直接输入标签名按tab键**

- 生成多个相同标签：标签名加*号，例如:

  ```
  div*4
  // 中间不要有空格
  ```

   

- 父子关系，直接使用`>`表示：

  ```
  ul>li
  
  生成结果：
  <ul>
    <li></li>
  </ul>
  
  div>span ...
  ```

- 兄弟关系的标签，直接使用`+`号

  ```
  div+p
  
  生成结果：
  <div></div>
  <p></p>
  ```

- 生成带有类名以及ID名的，直接`.class`或者`#id`

  ```
  div.class:
  <div class="class"></div>
  
  div#id:
  <div id="id"></div>
  ```

- 如果生成的 div 类名是有顺序的，可以使用自增符号 `$`:

  ```
  //生成两个div：
  div.cls$*2
  
  生成效果：
  <div class="cls1"></div>
  <div class="cls2"></div>
  ```

- 如果标签内默认有文字：

  ```
  div{3333}
  生成结果：
  <div>333</div>
  
  div{333}.demo$*2
  生成结果：
  <div class="demo1">333</div>
  <div class="demo2">333</div>
  
  
  div{$}*2:
  <div>1</div>
  <div>2</div>
  ```

  

##### 2.快速生成CSS样式

基本采取简写形式：（多个单词的时候，每个单词的首字母）

```
w200 -> width: 200px
lh 26 -> line-height: 26px
```



#### 快速格式化代码

vscode “格式化文档”， `Alt+shift+F`

1. 首选项

2. 在“用户区”搜索 "emmit.include"

3. 找到 Emmet: Include Languages

4. 修改json文件或者“添加项”

   ```
   editor.formatOnType: true,
   editor.formatOnSave: true
   ```

**上述为旧版本方法**

新版本VScode直接搜索 `format`

在`文本编辑器`中找到`Editor: Format On Save`，勾选即可



#### 复合选择器

基本选择器组合形成

- 后代选择器
- 子选择器
- 并集选择器
- 伪类选择器



##### 1.后代选择器（重要）

选择父元素中的子元素

格式：

```
元素一 元素二{
 xxx
}

eg:
ul li{
  xxx
}
```

- 元素二和元素一之间**使用空格隔开**
- 元素二必须是元素一的后代 （儿子，孙子等）
- 最终选的是元素二
- 元素一和二可以是任意的元素选择器



##### 2.子元素选择器（重要）

选择某一元素的**最近一级**（亲儿子）元素

```
元素一 > 元素二{

}
eg:

css:
div>a{
  color: red;
}

html:
<div>
  <a href="#">aaa</a>
  <a href="#">bbb</a>
  <p><a href="#">ccc</a></p>
</div>

//这种情况下只有第一个<a>aaa</a>会改变样式
```

- 选择直接后代元素
- 使用`>`隔开



##### 3.并集选择器

选择多类标签，设置统一样式

- 通过**英文逗号分隔**
- 任何元素都可以使用并集选择器
- 所有元素**竖着写**
- 最后一个元素不用加逗号

```
div
p
.class_a li{

}
```



##### 4.伪类选择器

向某些选择器添加特殊效果，例如向链接添加特殊效果，选择第1，第N个元素等

**特点**： 使用`:`表示，`:hover`, `:first-child`等

###### 链接伪类选择器

语法：

```
a:link - 未被访问
a:visited - 已经被访问
a:hover - 鼠标指针位于其上
a:active - 鼠标按下未弹起
```

**注意事项**

- **按LVHA的顺序书写**

- 实际中一般对链接单独指定样式

- 实际中一般只使用部分（只对 hover 状态进行设置）

  ```
  //首先对所有链接设置未选中的样式
  a{
    color: gray;
  }
  
  //设置鼠标经过时的样式
  a:hover{
    color:red;
  }
  ```

  

###### focus伪类选择器

选择获得**焦点(鼠标指针)的表单元素**，主要针对 input 表单元素进行设置

```
input:focus{
  backgournd-color:pink;
}
```



##### 总结

- 后代选择器：使用空格隔开，使用较多
- 子代选择器：使用`>` 使用较少
- 并集选择器：使用逗号隔开 使用较多
- 链接伪类选择器：LVHA 使用较多
- focus伪类选择器：针对input类的表单元素 使用较少



#### 元素显示模式

元素显示模式：元素以什么方式进行显示，例如 div 占一行， 一行可以放多个 span

- 块元素 div
- 行内元素 span



##### 1.块元素

h1, div, p, ul, ol, li 等等

- 独占一行
- 高度，宽度，内外边距都可以控制
- 宽度默认是容器（父元素）的宽度的100%
- 是一个容器盒子，可以放其他的元素

**注意**

- 文字类的元素内不能使用块级元素
  - p，h1-h6 元素主要用于存放文字，因此里面不能放块级元素
  - p div /p 这种情况下生成的网页实际上会产生两个 p : p div p 

##### 2.行内元素 (内联元素)

a, strong,em, i, del, span 等

- 相邻的行内元素在一行上，一行可以显示多个
- **高度和宽度直接设置是无效的**
- 默认宽度就是本身内容的宽度
- 行内元素只能容纳文本或其他行内元素

**注意**

- 链接里不能再放链接
- 特殊情况下 a 可以放块级元素，但是先将 a 转为块级模式最安全

##### 3.行内块元素

img, input td 同时具有块元素和行内元素的特点

- 一行可以放多个这类元素 （行内）
- 默认宽度就是本身内容的宽度 （行内）
- 高度，宽度内外边距都可以控制 （块）



##### 总结

- 块级元素
  - 一行只放一个
  - 可以设置高度、宽度、内外边距
  - 默认宽度是父元素的100%，（如果指定宽度，改行也会被占用）
  - 可以包含其他标签
- 行内元素
  - 一行放多个
  - 不能直接设置高度、宽度，内外边距
  - 可以放文本或其他行内元素
- 行内块元素
  - **一行放多个**
  - 可以放多个行内块元素
  - **可以设置宽度、高度等**
  - 默认宽度为内容的宽度

##### 文字垂直居中

设置**文字行高等于盒子高度**

```
line-height: box_height
```

(使用上间距和下间距调整距离)

#### 显示模式转换

将行内元素转换为块级元素

- display: block 
- display: inline （用到较少）
- display: inline-block 



#### 背景

背景颜色，图片，平铺等等

- background-color : 默认 transparent

- background-image: 常用于表现 logo，装饰性的图片，大背景图片（方便控制位置）

  ```
  background-image: none/url("url")
  ```

##### 1.背景平铺

```
background-repeat:

1. repeat
2.no-repeat
3.repeat-x
4.repeat-y
```

##### 2.背景图片位置

background-position 改变背景图片的位置

```
background-position: x y;
```

- 使用x y坐标
- 使用方位名词或者精确单位

###### 参数使用方位名词 （left，center，right，top，bottom）

- 前后顺序无关 （left right一定是水平，bottom一定是垂直方向）
- 如果只指定一个参数（水平或者垂直方向），则第二个方位默认为居中对齐



设置背景图片的优势：

```
background-image: url("xxx");
background-repeat: no-repeat;
background-position: center top;
```

这种情况下，即使因为屏幕大小问题，无法将图片全面显示，也可以保证图片最中心位置能显示出来，并且在缩小浏览器后也能保证中心显示（水平设置居中，垂直设置top）



###### 参数使用精确单位 （px ，em）

- **第一个参数必须是x坐标，第二个参数必须是y坐标**（不可交换位置）
- 省略一个时，定义的必定是x坐标，并且y坐标默认为垂直居中



###### 参数使用混合单位

- 精确单位和方位名词混合使用，则第一个必定是x坐标，第二个是y坐标
  

##### 3.背景图像固定（背景附着）

background-attachment  -- 制作视差滚动

```
background-attachment: 
scoll: 背景图像随着对象内容滚动
fixed： 背景图像固定位置
```

##### 4 背景复合写法

习惯顺序：

1. 背景颜色
2. 背景图片地址
3. 背景平铺
4. 背景图像滚动
5. 背景图片位置

**所有的属性使用空格隔开**

##### 5 背景色半透明

```
background: rgba(0,0,0,0.3)
```

最后一个参数表示透明度 alpha

实际情况中会把 0 省略掉

```
background: rgba(0, 0, 0, .3)
```

设置半透明后，是盒子的背景半透明，盒子的内容不受影响



##### 背景总结

- background-color: 背景颜色
  - 预定义的颜色/十六进制/RGB代码
- background-image：背景图片
  - url()
- background-repeat：是否平铺
  - no-repeat, repeat, repeat-x, repeat-y
- background-position:背景位置
  - length/positiion x,y
- background-attachment：背景附着
  - scroll fixed
- 背景简写：合并所有属性
  - 背景颜色，背景图片地址，背景平铺，背景滚动，背景位置
- 背景色半透明
  - background: rgba(0, 0, 0, 0.3) （必须写四个值，最后一个0~1）

**背景图片的应用：**

实际常用于 logo 或者装饰性的小图片或者超大的背景图片，方便控制位置（精灵图等应用）



#### 三大特性

- 层叠性
- 继承性
- 优先级

##### 1.层叠性

相同选择器给设置相同的样式，一个样式就会覆盖另一个冲突的样式，层叠性主要用来解决冲突的问题

原则：

- 样式冲突：**遵循就近原则**，哪个样式离结构近，就执行哪个样式
- 样式不冲突，不会层叠

例如，同样的标签：

```
div{
  color: aaa
}
div{
  color: bbb
}
```

则执行时，应用的样式是 bbb （可以通过 Chrome 查看哪个样式被忽略）

```
div {
  background-color: red;
  font-size: 25px;
}

div {
  background-color: pink;
}
```

此时，font-size 不会被忽略，只有冲突的属性才会被忽略

##### 2.继承性

子标签会集成父标签的某些样式，如文本大小和颜色等。

```
div {
  background-color: lightgreen;
  font-size: 25px;
}
```

如果 div 中存在 p 标签文本，那么 p 标签文本的颜色同样也是 `font-size:25px`（可以在 Chrome 调试中看到， inherited from div）

可以继承的元素有 text-, font-, line- 开头的元素，以及 color 属性，边距等不会被继承。

**行高的继承**

行高属性可以不使用 `px` 单位，直接指定**倍数**，例如：

```
div {
  font-size: 30px;
  line-height: 1.5;
}

p {
  font-size: 14px;
}

...

<div>
  <p>test for paragraph.</p>
  <span>test for span.</span>
</div>
```

此时 p 标签继承 div 的 1.5 行高后，其具体行高计算为 `1/5 * 14 =21`，其行高为 `21px`。（自动调整行高）

##### 3.优先级

为同一元素指定多个样式

- 选择器相同，执行层叠性
- 根据选择器的权重执行



1. 继承或者`*`  [0,0,0,0]

2. 元素选择器  [0,0,0,1]

3. 类选择器，伪类选择器  [0,0,1,0]

4. ID选择器  [0,1,0,0]

5. 行内样式 style=  [1,0,0,0]

6. !important  [无穷大]

   ```
   div{
     color: pink!important;
   }
   ```

   

四个数字从左到右依次比较

**注意**

继承的权重是0，如果该元素没有直接选中，则不管父元素权重有多高，子元素得到的权重都是0.

##### 权重叠加

这种情况：

```
ul li {
  color: blue;
}

li {
  color: red;
}

...

<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
</ul>
```

这种情况下，不管先后顺序如何，`<li>`永远使用`ul li{}`的样式。原因是样式进行叠加：

```
ul li -> 0,0,0,1 + 0,0,0,1 = 0,0,0,2

li -> 0,0,0,1
```

**注意不会有进位！！**

a:hover = 0,0,1,1