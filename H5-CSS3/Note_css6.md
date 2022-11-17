## 进阶

### 精灵图

一个网页中往往会有很多小背景图作为装饰，但是网页中图像较多的时候，服务器会频繁接收和发送请求图片，造成服务器压力过大，降低页面加载速度。由此出现了CSS 精灵技术。

核心原理：将网页中的一些小背景图像整合到一张大图中，这样服务器只需要一次请求就可以了。

注意点：

1. 主要**针对背景图片使用**，将较多的小背景图片整合成一张大图片（产品类图片一般不使用，因为经常需要改变）
2. 整张大图片被称为sprites，精灵图或者雪碧图
3. 实现方法：**移动背景图片位置**，此时可以使用 `background-position`
4. 移动的距离就是这个目标图片的 x 和 y 坐标，注意网页中的坐标与数学坐标系不相同（向下，向右为正向）
5. 一般情况下从大图片的左上角出发，将图片向上向左移动，所以**数值都是负值**
6. 使用精灵图的时候需要精确测量每个小背景图片的大小和位置

案例：使用精灵图技术拼接出单词

1. 精确测量出图片的大小（盒子大小）

2. 指定 background-position 属性：

   ```
   background-position: -182px 0px;
   ```

   使用复合属性写法：

   ```
   .n {
     width: 108px;
     height: 119px;
     background: url(Materials/abcd.jpg) no-repeat -215px -141px;
   }
   ```

3. 因为所有的背景图片都相同，所以可以设置公共类简化代码

精灵图缺点：

1. 图片文件较大
2. 图片本身放大和缩小会失真
3. 图片制作完毕想要更换非常复杂



### 字体图标

字体图片效果类似图片，但是实际上属于文字，因此可以自由变换颜色，大小等属性。

- 一般简单的小图像使用字体图标实现
- 较为复杂的背景图片依然通过精灵技术来实现

字体图标的使用：

1. 字体图标可以在网上直接下载
2. 字体图标的引入（引入到HTML页面中）
3. 字体图标的追加（以后添加新的小图标）

资源网站：

- icomoon 

  ```
  https://icomoon.io
  ```

- 阿里iconfont字库

  ```
  http://www.iconfont.cn
  ```

  

使用方式：通过CSS进行字体声明：

```css
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
```

```
span{
	font-family: 'icomoon'
}
```



字体图标追加：

如果需要向原有的字体图标中追加新的字体图标，可以将原有的压缩包里的 `selection.json`文件重新上传，然后选择新的图标，最后重新下载压缩包并替换原来的文件即可。（icomoon网站）

字体图标优势：

服务器一次返回多个字体图标，减小服务器的压力。



### 三角形制作

通过 border 属性制作三角形：

```css
.box1 {
    width: 0;
    height: 0;
    line-height: 0;    // 照顾兼容性
    font-size: 0;
    border: 30px solid transparent;
    border-top: 30px solid #000;
}
```

1. 必须设置高度和宽度都是0
2. 其中三个方向的 border 设置为 透明
3. 最后设置剩下的一个 border 边框

原理：放大边框之后发现，四个边框是四个边拼接而成。



### 用户界面样式

更改一些用户操作的样式，以便提高更好的用户体验。

#### 改变鼠标样式

语法：

```
li{
  cursor: pointer;
}
```

用于设置在对象上移动的鼠标指针采用何种系统预定义的光标形状。

1. default 默认指针
2. pointer 手型指针
3. move 移动指针
4. text 文本
5. not-allowed 禁止

#### 表单轮廓线

去掉表单轮廓线

```
input{
  outline: none;
}
```

防止文本域拖拽

```
textarea{
  resize: none;
}
```

#### vertical-align

经常用于设置图片或者表单（行内块元素）和文字垂直对齐。**只针对行内元素或者行内块元素有效**

文本中自上而下有几条线：

1. 顶线（top line）字符最上沿
2. 中线（middle line）字符最中间
3. 基线（base line）字符基准，例如字母 a的最下沿，字符 j 勾的上沿位置
4. 底线（bottom line）字符最下沿

```
vertical-align: baseline(default), bottom, middle, top
```



#### 图片与边框之间存在空隙

原因：默认情况下图片与文本垂直方向是 基线对齐，这种情况下会为字符最下沿的部分预留位置

解决方案：

1. 设置`vertical-align`，不按照基线对齐（推荐）
2. 将图片转化为 block （行内块和行内元素才有 vertical-align）



#### 溢出的文字使用省略号显示

##### 1.单行文本

1. 强制文本在一行内显示：

   ```
   white-space: nowrap; (默认值为 normal - 换行显示)
   ```

2. 设置溢出内容不显示

   ```
   overflow: hidden;
   ```

3. 文字溢出时使用省略号显示

   ```
   text-overflow: ellipsis;
   ```

eg：

```css
.box5 {
    width: 200px;
    height: 200px;
    background-color: lightblue;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}
```

##### 2.多行文本

eg：

```css
.box6 {
    width: 100px;
    height: 65px;
    background-color: lightgreen;

    overflow: hidden;
    text-overflow: ellipsis;
    /* 使用弹性伸缩盒子模型显示 */
    display: -webkit-box;
    /* 限制在一个块级元素中显示的文本的行数（在第几行使用省略号） */
    -webkit-line-clamp: 3;
    /* 设置伸缩盒对象的子元素的排列方式 */
    -webkit-box-orient: vertical;
}
```

以上代码只适合 webkit 浏览器（Chrome）或移动端显示，实际中更推荐后台人员做这个效果，更简单。



### 常见布局技巧

#### 1. margin 负值应用

如果多个盒子设置浮动的时候，border 会进行叠加，使得盒子 border 变成两倍，可以设置所有的盒子 margin向左移动一倍边框宽度。

此外，CSS渲染时候会依次进行，所以不会出现所有盒子 “相对静止”。



#### 2. 鼠标经过盒子动态改变border

效果：鼠标经过某个盒子的时候，border会变颜色

使用上述方式实现边框统一粗细的时候，由于右侧盒子的左边框会压住左侧盒子的有边框，因此会出现 border 显示不完整的问题。

解决方案：

1. 如果盒子没有定位，可以直接设置盒子 `position: relative`（相对定位会压住标准流的盒子）
2. 如果盒子已经有定位，提高盒子的层级即可：`z-index:1`



#### 3.文字环绕浮动元素

效果 [ [图像] 文字]

原理：**浮动不会压住文字**

1. 定义一个 div
2. 直接在 div 中输入文本（标准流）
3. 左侧图像 div 直接设置浮动



#### 4. 行内块元素

行内块元素可以直接设置元素的高度和宽度，对于网页底部跳转页面页数的效果，可以对多个链接元素设置为行内块元素进行实现。



#### 5. CSS三角应用

通过调整不同方向的 border属性，可以得到不规则的三角形：

```css
.box3 {
    border-color: transparent red transparent transparent;  //只保留右边框有颜色
    border-style: solid;  
    border-width: 100px 50px 0 0;  // 上边框宽度要大 右边框稍小，其他边框设置为0
}
```

（左侧为斜边的直角三角形）



### CSS 初始化

保证兼容性，重设浏览器的样式默认值（不同浏览器有不同的样式默认值）

#### Unicode 编码字体

把中文字体的名称用相应的 Unicode 编码来代替，这样可以有效避免浏览器解释 CSS 代码的时候出现乱码

例如：

- 黑体： \9ED1\4F53
- 宋体：\5B8B\4F53
- 微软雅黑：\5FAE\8F6F\96C5\9ED1

```css
body {
    /* CSS3 抗锯齿形 让文字显示的更加清晰 */
    -webkit-font-smoothing: antialiased;
    background-color: #fff;
    font: 12px/1.5 Microsoft YaHei, Heiti SC, tahoma, arial, Hiragino Sans GB, "\5B8B\4F53", sans-serif;
    color: #666
}
```



