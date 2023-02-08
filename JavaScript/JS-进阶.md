# 技巧

## 1.获取对象的键、值

利用 `Object` 的方法快速抽取对象的键值为单独数组：

```js
const obj = {'a': 10, 'b': 12, 'c': 13}
const keyList = Object.keys(obj)
const valList = Object.values(obj)
const kvArr = Object.entries(obj)
```

其中 `Object.entries()` 将对象处理为二维数组形式，例如上述处理的结果为 `[['a', 10], ['b', 12],...]`

如果要处理的是对象数组，可以利用 `map` 方法为每一个进行处理		

## 2.使用map处理数组

使用 `map` 可以对数组中的每一项进行单独处理，例如 1 中提到利用 map 抽取对象数组的信息：

```js
const objs = [{'a':10, 'b':12}, {'c':11, 'd':17}]
let vals = objs.map((item)=>{
    return Object.values(item)
})
let keys = objs.map((item)=>{
    return Object.keys(item)
})
```

输出结果为二维数组：

```
[['a', 'b'], ['c', 'd']]
[[10, 12], [11, 17]]
```

或者对数组元素进行一些单独处理，例如：

```js
const arr = [1, 2, ,3, 4]
let arr2 = arr.map((item)=>{
    return item*item
})
```

上述代码将数组中每个元素改变为平方结果

## 3.ES6 箭头函数以及简写

箭头函数是对普通函数的简写形式，可用可不用，需要注意在其内使用 `this` 的特殊情况（箭头函数内 `this` 不会指向自身，而是其上一级调用）

箭头函数在某些情况下可以继续简写，规则为：

- 函数的参数只有一个时，括号可以省略，但是没有参数时，括号不得省略
- 函数体中有且只有一行 return 语句时，中括号以及 return 关键字可以省略

例如在 2 中，利用箭头函数对数组每个元素进行平方处理，可以简写为：

```js
let arr2 = arr.map(item => item*item)
```

- 参数为1  ----  小括号省略
- 函数体代码只有一行返回  ----  省略return 关键字以及 花括号

## ES6 let 和 const

相较于 `var` 更推荐使用 `let` 和 `const`

1. 使用`var`会出现变量提升的现象，即变量可以在声明之前使用，这不符合一般的逻辑，`let`改变了这一语法，使用变量之前必须声明
2. 代码块中，如果存在`let`或`const`，就会封闭当前的块级作用域，形成“暂时性死区”，表示在当前区域，在声明`let`之前，该变量都是不可用的。其目的在于减少运行时的错误，规范编码习惯
3. 块级作用域可以防止内层的变量覆盖外层变量，也可以防止内层变量泄露为全局变量（例如计数器变量）
4. `let`不允许重复声明同一个变量
5. `const`一经声明就不允许修改，因此必须在声明时进行初始化
6. `const`只在当前块级作用域起作用，不可重复声明
7. `const`实际上表示指针是固定的，因此对于赋值为对象的const，其内容是可以修改的
8. `var`所定义的变量会直接成为顶层对象的属性，这样在任意文件位置都可以修改，不利于模块化编程

## 几种循环方法

### foreach 方法

```js
const arr = 'abcd'.split('')
arr.forEach(item => console.log(item))
```

注意 forEach 方法循环不会提前结束，即使触发结束条件，并且在循环的过程中无法 return，因此如果在 forEach 的过程中进行函数返回，得到的结果是 undefined

### jq - $.each 方法

使用 jQuery 的方法，主要用于处理对象，例如：

```js
const obj = {name: 'zs', age:17, gender: 'man'}
$.each(obj, (index, item)=>{
    console.log(index, item)
})
```

### jq - $(ele).each 方法

主要用于处理 DOM 元素，例如为每个 `li` 元素添加背景色：

```js
const colorMap = ['#758d71', '#e7cd79', '#467897', '#800020']
$('li').each((index, item)=>{
    $(item).css('background', colorMap[index])
})
```

## 事件委派

向一组元素的公共祖先绑定事件，而在后代中触发事件的时候，会向上一直冒泡到祖先元素，例如向 `ol` 中的每个 `li` 元素添加点击改变背景颜色事件：

```js
$('ol').on('click', 'li', function(){
    $(this).css('background', '#758d71')
})
```

其中`on` 的第二个参数 `li` 即指定了 事件委派

解绑事件委派方法：`$('ol').off('click', 'li')`

## ES6 推荐编程风格

1. 使用`let`代替`var`

2. `let`和`const`之间推荐使用`const`，尤其是全局环境中应只设置常量，并且js编译器会对`const`做优化，有利于程序的运行效率，此外还可以提醒其他成员不应修改这个值，避免无意识修改变量值所带来的错误

3. 静态字符串一律使用**单引号或者反引号**，不使用双引号，**动态字符串使用反引号**：

   ```
   const str1 = 'abc'
   const str2 = `ab${c}d`
   ```

4. 使用数组成员对变量赋值时，优先使用解构赋值：

   ```
   const arr = [1, 2, 3]
   const [first, second] = arr
   ```

5. 如果**函数需要返回多个值，优先使用对象的解构赋值**（不要用数组解构赋值），这样有利于之后添加返回值，以及可能的更改返回值顺序：

   ```js
   function func1(){
       let a = 1
       let b = 2
       return {a, b}
   }
   $(function () {
       const {a, b} = func1()
       console.log(a)
       console.log(b)
   })
   ```

6. 多行定义的对象，最后一个成员以`,`结尾

7. 使用扩展运算符拷贝数组：

   ```
   const arr1 = [1, 2, 3, 4]
   const arr2 = [...arr1]
   ```

8. 使用`Array.from(arr)`将类似数组的对象转化为数组

9. 对于使用匿名函数当作参数的场合，尽量使用箭头函数代替，这样会更简洁，此外，简单的不会复用的函数，推荐使用箭头函数，如果函数较为复杂，则还是应该使用传统的函数写法

10. 函数的参数不确定时，不要再函数内使用`arguments`变量，而使用 rest 运算符代替:

    ```js
    function func2(...args){
        return args.join(' ')
    }
    ```

11. 使用`export`代替`module.exports`

### npm 错误：

使用命令：Cannot read properties of null (reading ‘pickAlgorithm‘)

```
npm cache clear --force
```

## 数组截取

可使用两种方法：

- arr.slice(a, b)
  - 截取 [a, b) 范围内的元素，不改变原数组
- arr.splice(a, b)
  - 截取 [a, b) 范围内的元素，并将此范围的元素从原数组中删除

例如，随机生成长度为 50 的数组，并划分为长度为 5 的数组：

```js
// 生成随机数数组
function getRandArr() {
  let testArr = []
  for (let i = 0; i < 50; i++) {
    // 生成随机数并截取前 3 位
    testArr.push(parseFloat((Math.random() * 10).toFixed(3)))
  }
  return testArr
}

function arrSplit() {
  const arr = getRandArr()
  let splitArr = []
  for (let i = 0; i < 10; i++) {
    splitArr.push(arr.slice(i * 5, (i + 1) * 5))
  }
  return splitArr
}
```





