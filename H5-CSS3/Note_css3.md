## Note 3

### box Model

网页布局三大核心：

- 盒子模型
- 浮动
- 定位

网页布局过程：

1. 准备网页元素（盒子模型）
2. 使用CSS设置盒子样式，摆放到相应位置
3. 往盒子里面装内容



### 组成部分

将HTML页面中的布局元素看作是一个矩形盒子，封装周围的HTML元素，它包括边框，外边距，内边距和实际内容。

组成部分：

- border
- content
- padding 内边距
- margin：外边距

margin-border-padding-content



### border

组成：

- 宽度 ： border-width

  ```
  xxxpx
  ```

  

- 样式:  border-style

  ```
  solid, dotted, dashed
  ```

  

- 颜色: border-color



复合写法：

```
border: 1px solid red
```

边框分开写法：

```
border-top: 5px solid black;
botder-bottom:1px dashed blue
```

案例 ：

设置一个200X200的div， 上边框为red，其他边框为blue：

- 方法一：单独设置每条边框

- 方法二：利用样式的层叠性：

  ```
  div{
    border: 1px solid blue;
    border-top: 1px solid red;
  }
  ```

  这种写法注意前后顺序（**就近原则**）（先写范围大的，后写范围小的）



### 表格细线边框

表格经常需要的设置：

```
table,
th,
td {
  border: 1px solid pink;
  /* 表格边框合并 */
  border-collapse: collapse;
  text-align: center;
  font-size: 14px;
}
```

主要是将相邻的边框合并在一起，防止两个边框重合带来的变粗问题。



### 边框

边框的宽度会影响盒子实际的大小，两种方案：

- 测量的时候不测量边框
- 测量时候包含了边框，需要设置 width 和 height 的时候去掉边框



### 内边距 padding

padding 属性用于设置内边距，边框与内容之间的距离：

- padding-left
- padding-right
- padding-top
- padding-bottom

复合写法：

```
padding:5px - 上下左右都是5px
padding: 5px 10px  -上下5 左右10
padding: 1px 10px 20px - 上5 左右10 下20
padding： 1px 10px 20px 30px  - 上右下左（顺时针方向） 
```

**（注意设置顺序）**

#### padding和内容

padding 也会影响盒子的大小，如果 box 已经有了宽度和高度，此时在指定 padding ，则会撑大盒子。

解决方案：

- width 和 height 减去多出来的 padding 大小

#### 用 padding 保持距离

制作导航栏常用的几个元素

- **将 a 标签转变为 inline-block**

- 使用padding-left, padding-right 撑起左右边距

  ```
  padding: 0px 10px;
  ```

  

- 在 div 内使用 text-algin 将内部元素居中   

- **注意 padding 设置之后，修改原本的 heihgt 和 width**

#### padding 没有撑开盒子的情况

如果没有指定 heihgt width 的时候，padding 则不会撑开，而**只要设置了 width 属性，padding就会生效**（即使是 设置 `width:100%`也会增加 padding ）



### 外边距margin

用于控制盒子和盒子之间的距离

语法与 padding一样，简写情况下按照顺时针进行设置。

#### 盒子水平居中

- 盒子指定宽度
- 左右 margin 设置为 `auto`
  - margin-left: auto, margin-right: auto
  - margin: auto
  - **margin: 0px auto** （推荐写法）

#### 居中

对于块级元素：

- 设置 `margin-left: auto` 或者 `margin-right: auto`

对于行内元素或者行内块元素：

- 设置其**父元素** `text-aligin: center`即可

#### 嵌套块元素塌陷

场景：

```css
.father {
    background-color: lightblue;
    height: 300px;
    width: 300px;
    margin-top: 50px;
}

.son {
    margin-top: 100px;
    height: 100px;
    width: 100px;
    background-color: lightgreen;
}
```

其中 father 包含 son ，为两个 div 元素，这时候会发现 son div 的 margin-top 并没有生效， 反而 father div向下移动，移动结s果有两种情况：

- son 的 margin-top 如果大于 father 的margin-top，则 father 向下移动更大（为 son 的 margin-top 值）
- son 的 margin-top 小于 father 的 margin-top，则直接使用 father 的 margin-top 值（相当于 son 的 margin-top 失效）

**解决方案**：

1. 为父元素定义 border-top
2. 为父元素定义 padding-top
3. 为父元素添加 `overflow: hidden` **(最常用方案)**
4. （如果设置了浮动，固定或者绝对定位，则不会出现塌陷的情况）

#### * 清除内外边距

网页会有默认 padding 值，所以设置：

```css
*{
  padding: 0px;
  margin: 0px;
}
```

**NOTION**

行内元素为了保证兼容性，尽量只设置左右内外边距，不要设置上下内外边距（设置了也不起作用），只是转换为 **块级** 或者 **行内块元素** 就可以了

### PS相关操作

使用PS工具对效果图进行测量

- 文件 -> 打开：打开测量的图片
- Ctrl + R：打开标尺工具
- 右击标尺，将单位改成像素
- Ctrl + + 可以放大视图，使用-号可以缩小视图
- 按住空格键，将鼠标变成小手，可以拖动 PS 视图
- 用选区拖动，可以测量大小
- Ctrl + D 可以取消选区，或者在空白的地方点击一下取消选区 
- 使用吸管工具查看图片颜色



### 注意点

- 上下边距更多使用 margin 来控制
- 左右边距则是：
  - 不设置 width
  - 上下padding 0px 设置左右 padding xpx

Q1：布局被什么用不同元素

标签都是由语义的，例如标题一般都使用 h123，大段文字就用 p

Q2：为什么用多个类名

为每个盒子起名字，选取盒子更容易，后期维护更简单

Q3：margin 和 padding

大部分时候可以混用，使用更简单的方法实现

Q4：思路

布局有多种实现方式，可以通过多种风格实现

注意多用辅助工具