# 数组

相关联的数据一起存放

数组创建方式：

- 使用 new 关键字：

  ```
  var arr = new Array();
  ```

- 利用数组字面量创建：

  ```
  var arr = []
  ```



数组元素类型：可以存放任意类型的数据，例如字符串，数字以及布尔值等：

```
var arrStus = ['a', 123, true];
```

数组通过索引下标使用： `arr[1]`



数组长度：`arr.length`（可读写）

向数组中追加元素：直接修改索引号：

```javascript
var arr1 = [1, 2, 3];
console.log(arr1.length);
for (var i = 0; i < arr1.length; i++) {
    console.log(arr1[i]);
}
arr1[3] = 10;   //此时arr1.length = 4
console.log(arr1.length);
for (var i = 0; i < arr1.length; i++) {
    console.log(arr1[i]);
}
```

遍历数组 + 元素判断：将某个数组中大于5的元素保存到另一个数组中

```javascript
var arr = [1, 7, 3, 8, 2, 10];
var newArr = [];
for (var i = 0; i < arr.length; i++) {
    if (arr[i] > 5) {
        // 注意这里准确写法为 newArr[newArr.length] = arr[i]
        // newArr 中加入新元素后 length属性会变化，而写成 length++ 也正确的原因是 每次重新获取 length属性之后会刷新这个值
        newArr[newArr.length++] = arr[i];
    }
}
```



# 函数

封装重复的代码块

```
function func_name(var ...){

}
```

使用：

1. 声明函数 （**一般函数名都使用动词**）
2. 调用函数

形参和实参：

- 形参：声明函数的时候使用的参数
- 实参：实际调用时候传入的参数



形参和实参数量不匹配的问题：

- 形参数量 < 实参数量 ：取前形参数量
- 行参数量 > 实参数量： 未定义的部分为 `undefined` 计算结果会变成 `NaN`
- 因此， 尽量保证形参和实参数量相同

关于 `return`：

- `return`只会返回一个值（最后一个）
- 如果需要返回多个值，可以借助数组实现
- `return`之后的语句不会执行
- 没有 return 返回值是  undefined

## arguments

如果不确定有多少个参数要传递的时候，可以使用 arguments来获取，js中arguments是函数的一个内置对象，存储的内容是传递的所有实参

使用方式：

```javascript
function func() {
    console.log(arguments);
    console.log(arguments.length);
    console.log(arguments[2]);

    for (var i = 0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}

func(1, 2, 3);
```

arguments是一个伪数组，并不是真正意义上的数组：

- 具有数组的 length 属性
- 可以按照索引方式进行存储
- 没有真正数组的一些方法，例如 pop() push（）方法等



## 函数的两种声明方式

1. 利用函数关键字 `function`声明函数

2. 函数表达式（匿名函数）：

   ```javascript
   var fun = function (num1, num2) {
       return num1 + num2;
   }
   
   var res = fun(1, 3);
   console.log(res);
   ```

   其中`fun`是变量名而非函数名，匿名函数的声明方式与变量差不多，只不过里面存的是函数，函数表达式同样可以传递参数







