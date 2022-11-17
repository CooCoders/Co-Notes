## Example

### 1. 准备工作

1. 样式与结构相分离
2. 创建 study 文件夹，存放项目
3. 建立 image 文件夹



清楚默认 padding 和 margin：

```css
* {
    margin: 0px;
    padding: 0px;
}
```

### 2. CSS样式书写顺序

建议使用以下顺序：

1. 布局定位属性：

   ```
   display / position / float / clear / visiblility / overflow 
   ```

   建议 display 第一个写 ，毕竟关系到模式

2. 自身属性：

   ```
   width / height / margin / padding / border / background
   ```

3. 文本属性：

   ```
   color / font / text-decoration / text-align / vertical-align / white-space / break-word
   ```

4. 其他属性 （CSS3）

   ```
   content / cursor / border-radius / box-shadow / text-shadow / background:linear-gradient ... 
   ```

   

书写样例：

```css
.eg {
    /* 第一类：布局定位 */
    display: block;
    position: relative;
    float: left;

    /* 第二类：自身属性 */
    width: 100px;
    height: 100px;
    margin: 0px 10px;
    padding: 20px 0px;

    /* 第三类：字体属性 */
    font-family: Arial, Helvetica, sans-serif;
    color: #333;

    /* 第四类：CSS3属性 */
    background: rgba(0, 0, 0, .5);
    border-radius: 10px;
}
```



### 3.页面布局的整体思路

整体思路：

1. 首先确定页面的版心（可视区）（测量可知）
2. 分析页面中的行模块，以及每个行模块中的列模块（页面布局第一准则）
3. 一行中的列模块经常浮动布局，首先确定每个列的大小，之后确定列的位置（页面布局第二准则）
4. 制作 HTML 结构，遵循：**先有结构，后有样式的原则** ，结构永远最重要
5. 先清楚**布局结构**，再写代码



### 4. Header 部分

#### 4.1 整体规划

首先确定版心宽度为 1200px （测量），由于每个版心都需要水平居中对齐，可以定义版心为**公共类**：

```
.w {
    width: 1200px;
    margin: auto;
}
```



需要居中对齐的时候，都引用这个类即可。

分析整个 header 区域，可以看作是一个大盒子，内有四个小盒子（logo， nav， search 和 user）

- 版心盒子header （1200X42）水平居中对齐，上下分别有一个margin
- 版心盒子里面包含 盒子logo
- 版心盒子里面包含 盒子nav （导航栏）
- 版心盒子包含 盒子search （搜索框）
- 版心盒子包含 盒子user（个人信息）
- 上述四个盒子都需要浮动



#### 4.2 导航栏制作

实际开发中，不会直接使用链接 a 制作，而是 **使用 li 包含链接 （li+a）的做法**。

- li + a 语义更清晰，一看就是有条理的列表型内容
- 如果直接使用 a 标签，搜索引擎容易辨别为有堆砌关键字嫌疑（故意堆砌关键字会被搜索引擎降权），从而影响网站排名



**注意点**

1. 采用（li+a）的方式，设置导航栏一行显示的时候，要设置给 li 加浮动，而不是给 a 设置浮动（a 本身就是行内元素，li 是块级元素）
2. nav 导航栏不要设置宽度（方便之后修改，继续扩充添加其他文字）
3. 因为导航栏里的文字不一样多，所以最好给链接 **设置左右 padding 撑开盒子**，而不是指定宽度
4. 采用（li+a）方式，设置左右 margin 的时候，最好是对 li 进行设置
5. 对 a: hover 进行设置的时候，为了保证下划线类的样式随着文字长度变化而变化，可以对 a （display：block）后的下边框进行设置



#### 4.3 搜索框

整体结构分为左右两部分：

```
div.search
  1. input
  2. button
```

**注意点**

1. 盒子内部的文字等内容往往不是紧贴左右边框的，因此会设置 padding 属性，此时为了**保证 盒子的宽度不变，要回头再从盒子的宽度中减去 padding 距离**
2. button 存在默认 border，所以要对其设置 `border: 0px`
3. **input  和 button 都属于行内块元素** 摆放在一行内的时候中间会有一定的距离，如果要**去掉这部分距离，可以将两个元素都设置浮动**



### 5.Banner 部分

整体布局：

- 1号盒子是通栏大盒子 banner，不给宽度，给高度，设置蓝色背景
- 2号盒子是版心，水平居中对其
- 3号盒子版心内，左对齐 subnav 侧导航栏
- 4号盒子版心内，右对齐 course 课程

整体结构：

```
[div 1    [div 2 [div 3] [div 4] ]       ]
```



**注意点：**

1. 存在较多的 a 链接，不要直接设置 a 的样式，而是包裹在 li 标签中，对li标签进行设置。
2. 对于每一个 li ，只设置 height，不要设置 width，使用左右 padding 将盒子撑开即可（这一效果也可以对父元素设置 padding 实现，但是需要重新计算盒子的宽度）
3. 浮动的盒子不会有外边距合并的问题
4. 设置 padding 时候一定要注意设置的是 **左右padding** 还是 **上下padding**， 往往采用对称的设置，而不是 `padding: 10px` 这样



### 6. 精品推荐部分

整体布局：

```
[div goos [div1 H3]  [div2 link]    [div mod] ]
```

1. 大盒子 goods 水平居中（注意设置盒子阴影）
2. 盒子1 为标题，H3 设置左侧浮动
3. 盒子2 为链接，左侧浮动
4. 盒子3 mod 右浮动



**主题模块：**

1. 大盒子版心水平居中对齐
2. 盒子2为标题部分， box-hd 里面标题 h3左浮动， 链接 a 右浮动
3. 盒子3为主体部分，box-bd ，里面是无序列表，里面右10个 li 组成
4. 对 li 的外边距进行设置， 将 box-bd宽度设置为1215 

### 底部

整体结构：

```
[div1 [div2  [div3]  [div4]]]
```

其中：

1. 盒子1 为底部通栏盒子，背景颜色为白色
2. 盒子2版心水平居中
3. 盒子3版权 copyright左对齐
4. 盒子4链接组 links 右对齐



**注意点：**

1. 在写下一模块的时候，注意清除上模块的浮动
2. 注意 a 标签设置为 块级元素
3. 注意样式层叠





