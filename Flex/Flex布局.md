Flex 布局

## 原理

flexible box 弹性盒子，任何一个容器都可以指定为 flex 布局（块级元素或行内元素）

为父盒子设置 flex 布局之后，子元素的 float、clear 和 vertical-align 属性都会失效

采用 flex 布局的元素被称为 flex 容器（flex container），它所有的子元素自动成为容器成员，被成为 flex 项目（flex item）

总之 ，flex 布局就是通过给父盒子添加 flex 属性，来控制子盒子的位置和排列方式

## 常见的父属性

### flex-direction： 设置主轴方向

flex-direction 属性决定项目的排列方向，默认情况下，以x轴（水平向右）为主轴，以y轴（竖直向下）为侧轴

注意主轴和侧轴可以调整，设置某个方向为主轴后， 剩下的就是侧轴：

- row：默认值，自左向右
- row-reverse：自右向左
- column：自上而下
- column-reverse：从下到上

注意设置两个 reverse 的时候，元素的排列方式与书写顺序一致，例如：

```html
<span>a</span>
<span>b</span>
<span>c</span>
```

设置 `flex-direction: column-reverse`，则三个元素的顺序是：

```
[c]
[b]
[a]
```

### justify-content：设置主轴上的子元素排列方式

justify-content 属性定义了项目在主轴上的对齐方式（需要先确定主轴方向）

- flex-start (start)：默认值，从主轴的头部开始（默认下即从左到右）
- flex-end（end）：从尾部开始排列
- center：在主轴居中对齐
- space-around：平分剩余空间
- space-between：先两边贴边然后剩余元素平分剩余空间

### flex-wrap：设置子元素是否换行

flex 布局默认情况下不换行，如果元素总宽度（高度）超过父盒子，则会 “压缩” 每个子元素，使之排列在一行（列）内，使用`flex-wrap`设置是否换行显示

- nowrap：默认值，不换行
- wrap：换行

### align-content：设置侧轴上的子元素排列方式（多行）

如果子元素超过一行显示（wrap），使用 align-content 属性进行设置（**注意该属性必须在子元素多行情况下使用，单行情况下无效**）：

- flex-start：默认值，按侧轴正方向排列
- flex-end：按侧轴反方向排列
- center：侧轴居中
- space-around：在侧轴平分剩余空间
- space-between：首先在首位放置两个元素，剩下的元素平分剩余空间
- strtch：设置子元素平分父元素高度

### align-items：设置侧轴上的子元素排列方式（单行）

 控制侧轴子元素的排列方式：

- flex-start：默认，从上到下排列
- flex-end：从下到上
- center：居中
- strtch：拉伸子元素宽度（高度），根据实际情况要去掉子元素的宽度或者高度属性

### flex-flow：复合属性，等同同时设置 flex-direction 和 flex-wrap

例如：

```
flex-direction: row;
flex-wrap: wrap;
```

可以简写为：

```
flex-flow: row wrap;
```

## flex布局 子元素的常见属性

### flex 子元素占的份数

子元素设置的属性：

```css
element{
	flex: 1;
}
```

值表示所占的份数，即按照所有元素的份数进行划分，例如

a 元素： flex 1

b 元素： flex 2

则将空间分为 （1+2=3）份，然后 a 元素占一份，b 元素占两份



### align-self 控制子元素自己在侧轴的排列方式

align-self 用于设置单个子元素与其他子元素有不一样的对齐方式，可以覆盖 align-items 属性，默认为 auto，表示继承父元素的 align-items 属性，如果没有父元素则设置为 stretch，例如：

```css
div>span:nth-child(3) {
    align-self: flex-start;
}
```

### order 属性定义子项的排列顺序

所有子元素的 order 属性默认为 0，可以为某个元素设置 order 为负数或正数移动其顺序：

```css
div>span:nth-child(2) {
    order: -1;
}
```

这种方式不改动 dom 元素的位置，只是通过 css 修改展示的位置

## 其他

### 子元素向左右对齐

实现类似效果：

```
[[] [] [] []                  [E]]
```

可以为 E 元素指定样式：

```
margin-left: auto;
```

