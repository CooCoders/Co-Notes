## JS-Note

### 基本概念

运行在 **客户端**  的脚本语言

- 脚本语言：不需要编译，运行过程中由JS解释器逐行来进行解释并执行
- 现在也可以基于Node.js技术来进行服务器编程

JS作用：

- 表单动态验证
- 网页特效
- 服务端开发 （Node.js）
- 桌面程序
- App
- 硬件控制
- 游戏开发

HTML，CSS， JS之间的关系

HTML决定网页结构和内容

CSS决定网页呈现方式

JS实现业务逻辑和页面控制

### 浏览器执行JS

浏览器：

- 渲染引擎：用来解析HTML和CSS，俗称内核 （Chrome-blink）
- JS引擎：也称为JS解释器。用于读取网页的JS代码，对其进行处理后运（Chrome-V8）

浏览器本身不会执行JS代码，只是通过内置的JS引擎来执行JS代码，执行时逐行解释，转化为机器语言，然后由计算机去执行，所以JS语言归为脚本语言，会逐行解释执行

### JS 组成

组成：

- ECMAScript 语法： 定义了编程语法和基础核心
- DOM页面文档对象模型：对元素进行操作
- BOM浏览器对象模型：操作浏览器窗口，弹出框，跳转，获取分辨率等

书写位置：

- 行内
  - 卸载HTML标签属性中，例如on开头的属性
  - 注意单引号的使用：**HTML推荐使用双引号，JS推荐使用单引号**
  - 特殊情况下使用
- 内嵌
- 外部
  - 利于HTML页面代码结构化，将大段JS代码独立到HTML页面之外，美观，并且有利于文件级别的复用
  - `<script src="xx.js"></script>`
  - 两个 script 标签之间不可以写代码
  - 适合JS代码量较大的情况

### 注释

```
1. // 单行注释
2. /* */ 多行注释 （shift+alt+A）
```



### JS 输入输出

三种主要的输入输出方式：

1. 弹出警示框：

   ```
   alert("")
   ```

2. 弹出输入框,用户输入信息

   ```
   prompt("xxx")
   ```

3. 使用控制台输出信息：

   ```
   console.log('xx')
   ```

### 变量



定义：存放数据的容器（内存中申请的一块用于存储数据的空间）

变量的使用：

1. 声明变量

   ```
   var age;
   ```

   其中 var 为 JS 关键字，用来声明变量，使用该关键字后，计算机自动为变量分配内存空间， 其中 age 为定义的变量名，需要使用变量名来访问内存中的空间

2. 赋值

   ```
   age = 10;
   ```

   

#### 1. 变量声明

多个变量同时声明：

```javascript
var var1 = 10,
    var2 = 20,
    var3 = 30;

console.log(var1);
console.log(var2);
console.log(var3);
```

几种变量声明情况：

1. 只声明不赋值 -- 结果为 undefined
2. 不声明不赋值 ，直接使用  -- 报错
3. 不声明直接使用 -- 可以使用（变量会变成全局变量，不推荐）



#### 2. 变量的命名规范

- 由字母，数字，下划线和美元符号（$）组成，例如：userAge, num01, _name
- 严格区分大小写
- 不能以数字开头
- 不能是关键字，保留字
- 变量名必须有意义
- 遵守驼峰命名法：首字母小写，后面单词的首字母都需要大写 （myFirstName）

注意：`name`虽然非关键字或保留字，但是在一些浏览器中是有含义的，所以不推荐使用`name`作为变量名



### 数据类型

JS 是一种弱类型（动态）语言，意味着不用提前声明变量类型，在程序运行过程中，类型会被自动确定。

```
var age = 10;   // 数字类型
var str1 = 'string'  //字符串
```

在代码运行的是哦胡，变量的数据类型是由 JS 引擎根据右边变量值的数据类型来判断的，运行完毕之后，变量就确定了数据类型。

JS 拥有动态类型，即相同的变量可以作用于不同的类型。



JS 数据类型分类：

- number： 数字型，包含整数型和浮点型（默认值 0）
- Boolean：布尔型，例如 true，false，等价于1，0（默认 false）
- String：字符串类型（默认 ”“）
- Undefined：声明变量但是未赋值
- Null： var a= null; 声明了变量 a 为空值（null）

#### 1.数字型

常使用的进制有二进制，八进制，十进制以及十六进制，其中：

- 声明八进制数字的时候 前面加 0: `var n = 011 (9)`
- 声明十六进制数字前面加 0x `var n = 0xA (10)`

最大数值和最小数值：

```
console.log(Number.MAX_VALUE);
console.log(Number.MIN_VALUE);
```

三个特殊值：

```
console.log(Number.MAX_VALUE * 2);   -- Infinity 代表无限大
console.log(Number.MIN_VALUE - 1);   -- -Infinity 代表无限小
console.log('string' - 10);   -- NaN 代表非数值
```

`isNaN()`  方法用于判断某一个变量是否为数字（true或false）



#### 2. 字符串型

使用单引号或者双引号包裹的变量。

- 推荐使用单引号
- 注意引号的嵌套

转义字符：

1. `\n`：换行符
2. `\\`：输出斜杠
3. `\'`：输出单引号
4. `\"`：输出双引号
5. `\t`：tab缩进
6. `\b`：空格

几中常用的字符串处理方法：

1. 字符串长度属性：`length`
2. 字符串拼接方法：字符串 + 任何类型数据 （最后**结果都是字符串**类型的数据），拼接的中间也可以是变量



#### 3. 布尔型

只有两个值 `true`或者`false`



#### 4. 特殊类型

undefined + 1 = NaN

undefined + 'a' = undefineda

null 也是值

```javascript
var tmp;
console.log(tmp);  // undefined
console.log(tmp + 'a');  // 'undefineda'
console.log(tmp + 1);  // NaN
console.log(tmp + true); //NaN


var tmp = null;
console.log(tmp + 'a');  // nulla
console.log(tmp + 1);  //1
console.log(tmp + true);  //true
```

#### 5. 获取变量类型

语法：

```
console.log(typeof var1);
```

```javascript
var num = 10;
console.log(typeof num);
var sstr = 'pink';
console.log(typeof sstr);
var flag = true;
console.log(typeof flag);
var var1 = undefined;
console.log(typeof var1);   //undefined
var var2 = null;
console.log(typeof var2);   //object

// prompt 获得的值是 字符串 类型
```

注意，通过Chrome控制台颜色：

- 蓝色：数字型
- 深蓝色：布尔类型
- 黑色：字符型
- 浅灰色：undefined ， null



#### 6.数据类型的转换

将一种数据类型的变量转化为另一种数据类型。

- 数字型转化为字符串：

  - `num.toString()`
  - `String(num)`
  - 使用`+`拼接字符串：**使用的最多** （隐式转换）

- 转化为数字型（重点）：

  - `parseInt()`（取整函数，只保留整数部分）

    ```javascript
    parseInt('120px') = 120
    paeseInt('px120px') = NaN
    ```

  - `parseFloat()`，对于带有字符的内容处理过程同上

  - `Number()`

  - `'12'-0`：隐式转换（利用 - * /）

    ```javascript
    '123' - '120' = 3
    '123' * 1 = 123
    ```

- 转化为布尔型

  - `Boolean()`函数

  - 只有五个值会转化为false，其他都会转化为true：

    ```
    '', 0, NaN, null, undefined
    ```

#### 7.编译型语言和解释型语言

编译型语言 - Java ： 运行时在代码执行之前收件使用编译器进行编译，生成中间代码文件 （编译器）

解释型语言- Javascript ： 运行时边运行边进行及时解释 （解释器）



#### 8. 标识符，关键字，保留字

- 标识符：开发人员为变量，属性，函数，参数等取的名字，标识符不能设置为关键字或者保留字
- 关键字：是指JS本身已经使用的字，例如 break，case catch， continue，var等
- 保留字：实际上就是预留的关键字，未来有可能成为关键字，例如 boolean ，byte， char等





















