# 动画

## offset 与 style

使用 offsetWidth，offsetTop等属性可以获得元素的动态高度宽度等属性

offset：

- offset可以得到任意样式表中的样式值
- 所得到的数值没有单位
- offsetWidth 包含 padding border width 属性
- 只读属性

style：

- **只能得到行内样式表**，因此一般情况下直接打印 width 属性返回的结果为空
- 所得数值带有单位
- 不包含 padding 和 border
- 可读写属性