# 作用域、预解析、对象

## 作用域

作用域：变量名在某个范围内起作用，提高程序的可靠性减小命名冲突

es6之前，两种作用域：

1. 全局作用域：整个js文件或者整个 script 标签之间
2. 局部作用域：函数内部

根据定义的作用域不同，变量的区别：

- 全局变量

  - 全局作用域下声明（var 定义）
  - **在函数内不使用var定义的变量也是全局变量**（不建议使用）
  - **注意：只有在浏览器关闭的时候才会被销毁，因此比较占内存**

- 局部变量

  - 在函数内部定义的变量
  - 只在函数内部使用，当所在的代码块被执行的时候，会被初始化，代码块运行结束后就会销毁，更节省内存空间
  - 现阶段没有块级作用域

  


 作用域链：内部函数访问外部函数定义的某个变量值，采用的是链式查找的方式（就近原则）：

```javascript
var num = 10;
function fun1() {
    var num = 20;
    function fun2() {
        console.log(num);   // 输出的结果为20
    }
    fun2();
}
fun1();
```



## 预解析

JS引擎运行JS代码分为两步：

1. 预解析
   1. 变量预解析（变量提升）：将所有变量声明提升到当前作用域最前面，不提升赋值操作
   2. 函数预解析（函数提升）：将所有的函数声明提升到当前作用域最前面，不调用
2. 代码执行 ：按照代码执行顺序从上往下执行

其中预解析会**将所有的 var 变量声明 和 function 提升到当前作用域的最前面**，例如：

```javascript
console.log(num);
var num = 3;
```

其运行的结果为 `undefined`，实际上代码运行的顺序是：

```javascript
var num;
console.log(num);
num = 3
```

对于匿名函数也是如此：

```javascript
fun();
var fun = function () {
    console.log('hi');
}

/*
        通过匿名函数方式等同于声明变量方式，因此上述代码执行步骤实际上是：
        var fun;
        fun();
        fun = function(){...}
        因此会报错
        */
```

由于函数提升：

```javascript
func();
function func() {
    console.log('hi');
}

```

代码运行正常

### 预解析案例

#### 案例一

```javascript
var num = 10;
fun();
function fun() {
    console.log(num);
    var num = 20;
}
```

输出结果为`undefined`，代码执行顺序等价于：

```javascript
var num;
function fun(){
    var num;
    console.log(num);
    num = 20;
}
num = 10;
fun();
```

注意两种情况：

```javascript
var num = 10;
fun();
function fun() {
    console.log(num);    // 10
}

var num = 10;
fun();
function fun() {
    var num;
    console.log(num);  // undefined
}
```



#### 案例二

```javascript
var num = 10;
function fn() {
    console.log(num);
    var num = 20;
    console.log(num);
}
fn();
```

输出结果为`undefined`和`20`，代码执行顺序为：

```javascript
var num;
function fn(){
	var num;
    console.log(num);
    num = 20;
    console.log(num);
}
num = 10;
fn();
```

注意是将变量声明和函数声明提升到**本作用域**的最前面。

#### 案例三

```javascript
f1();
console.log(c);
console.log(b);
console.log(a);
function f1() {
    var a = b = c = 9;
    console.log(a);
    console.log(b);
    console.log(c);
}
```

实际执行的代码为：

```javascript
function f1() {
    /*
            var a=b=c=9 相当于 var a=9; b=9; c=9; 因此 b，c都是全局变量
            abc全部声明为局部的方式为 : var a=9,b=9,c=9;
            */
    var a;
    a = 9;
    b = 9;
    c = 9;
    console.log(a);
    console.log(b);
    console.log(c);
}
f1();
console.log(c);
console.log(b);
console.log(a);
```

所以输出的结果为：

```
9 9 9 9（全局变量c） 9（全局变量b） 报错（a不是全局变量，未定义）
```



## 对象

JS对象：**一组无序的属性和方法的集合**

创建对象的方法：

1. 字面量（`{}`）
2. 使用new object
3. 使用构造函数



### 字面量方式

定义案例：

```javascript
var obj = {
    name: '可可',
    type: '阿拉斯加',
    age: 5,
    color: 'brown',
    bark: function () {
        console.log('wang wang wnag');
    },
    showFilm: function () {
        console.log('show film.');
    }
}

console.log(obj['name']);
console.log(obj.color);
obj.bark();
obj.showFilm();
```

注意点：

- 要使用 var 定义
- 属性和方法定义都采用 键值对 的形式
- 不同属性和方法之间使用 `,`隔开
- 方法的定义采用的是匿名函数的方式

对象调用：

- 对象.属性名
- 对象[‘属性名’]，属性名必须加引号
- 对象.方法名()，调用方法要加括号



### new Object方式

案例：

```javascript
var obj = new Object();
obj.uname = 'name';
obj.age = 18;
obj.sayHi = function () {
    console.log('Hi');
}

// 调用
console.log(obj.uname);
console.log(obj['age']);
obj.sayHi();
```

利用等号赋值的方式添加对象属性，每个属性和方法之间使用`;`隔开。



### 构造函数方式

构造函数：将对象里一些相同的属性和方法封装到函数中

案例：

```javascript
function People(name, age, sex) {
    this.name = name;
    this.age = age;
    this.sex = sex;
    this.func1 = function () {
        console.log('hi.');
    }
}

var people1 = new People('name1', '18', 'man');
console.log(typeof (people1));  // object 类型
console.log(people1.name);
console.log(people1.sex);
people1.func1();
```

注意点：

- **构造函数名一定要大写**
- **构造函数不需要 return** 就可以返回结果
- **调用构造函数必须使用 new**
- 注意 `this`
- 通过 new 关键字生成对象也成为对象实例化

New 关键字的执行过程：

1. 首先在内存中创建一个新的空的对象
2. 让 this 指向这个空对象
3. 执行构造函数里的代码，给这个新对象添加属性和方法
4. 返回这个新对象  -- （构造函数不需要返回）

通过构造函数的方式创建对象，可以方便的创建多个对象



### 遍历对象

使用 for-in 语句可以遍历数组或者对象（**推荐用来遍历对象**）

案例：

```javascript
var obj1 = new Object();
obj1.name = 'uname';
obj1.sec = 'man';
obj1.age = 18;
obj1.func = function () {
    console.log('say hi');
}

for (var k in obj1) {  // 一般都会使用 k 或者 key
    console.log(k);  // k 表示的是对象中的属性名
    console.log(obj1[k]);  // obj[k]可以得到对象的某个属性值 注意不加引号
}
```



## 内置对象

三种对象：

- 自定义对象：自己创建
- 内置对象：JS中预先写好的对象，给开发者提供的一些基础功能，从而直接调用使用
- 浏览器对象

常用内置对象

### Math

Math对象所有的方法和属性都是静态的，因此直接调用即可，并且无法通过构造方法创建实例对象

提供了一些数学常量和数学计算方法，例如：

- Math.PI
- Math.max()
- Math.abs()
- Math.floor() 向下取整
- Math.ceil() 向上取整
- Math.round() 四舍五入
- Math.abs()

注意 Math.max(val1, val2....)， 如果是从整个数组中取得最大值，则使用:

```
var arr = [1,2,3,4];
var maxele = Math.max(...arr)
```

`...arr`表示 spread operator

通过 Math 封装自己的数学对象

```javascript
var myMath = {
    PI: Math.PI,
    max: function(){
        max = arguments[0];
        for(var i=1; i<arguments.length; i++){
            if(max < arguments[i]){
                max = arguments[i];
            }
        }
        return max;
    },
```

### Date

Date对象提供了多种构造函数

- 使用空参构造函数：输出当前系统的当前时间

- 使用字符串或数字形式传入参数，例如：

  ```javascript
  var nowDate = new Date();
  console.log(nowDate);
  
  var date1 = new Date('2022-07-19 13:44:10');
  console.log(date1);
  
  var date2 = new Date(2022, 7, 19);
  console.log(date2);
  
  var date3 = new Date('2022/7/19');
  console.log(date3);
  ```



日期格式化：可以通过 Date.getMonth() 等方法得到日期的具体某一项内容

- Date.getFullYear()
- Date.getMonth(） 注意月份的计算是从 0 开始的，因此范围是 0-11
- Date.getDate()
- Date.getDay()   注意周末是0索引

时刻格式化：使用类似方法

- Date.getMinutes()
- Date.getHours()
- Date.getSeconds()

取得对应值后进行格式化，如果需要 03:03:03 类似格式的时刻，可以使用三元表达式获得：

```
var date = new Date();
var hour = date.getHours();
hour = hour>10?hour:'0'+hour;
```

获得自 1970年开始的总毫秒数

- date.getTime()
- date.valueOf()
- var date = +new Date()
- Date.now()    (H5新增的，获得总的毫秒数)

由于时间戳是唯一的，因此可以用于加密等需求



### Array

创建数组的方式：

- 字面量方式 var arr = [1,2,3]

- new Array()

  ```
  var arr = new Array(2, 3)
  ```



判断对象是否属于 Array：

```
var arr = [];
// 使用 instanceof 运算符
console.log(arr instanceof Array)

// 使用 Array.isArray()方法 将待判断对象作为参数传入
console.log(Array.isArray(arr));
```

对数组对象进行元素改变：

- arr.push()  在数组末尾添加一个或者多个元素 （直接对原始数组修改）返回新数组的长度

  ```
  arr.push(1, 'a')
  ```

- arr.pop()  删除数组的最后一个元素，直接修改原始数组，返回删除的元素，一次只能删除一个元素

- arr.unshift()  向数组的开头增加一个元素，修改原始数组

- arr.shift()  在数组开头删除一个元素，修改原始数组，返回删除的元素的值

- arr.reverse()  对原始数组进行反转

- arr.sort()  对原始数组进行排序

其中要注意 arr.sort() 方法，其进行排序的原理是首先比较所有数字做左边的一位数，因此如果出现[1,11,3] 这时候排序的结果是 [1, 11, 2]。正确排序的调用方式：

```javascript
var arr = [1, 11, 2, 23, 3, 33]
arr.sort(function(a, b){
	return a - b;  // 升序排序
    return b - a;  // 降序排序
});
```

获取数组的某个元素的索引：

- arr.indexOf(element):   返回数组中某个元素的第一个索引 （如果没有对应的元素，则返回-1）
- arr.lastIndexOf(element):  返回数组中某个元素的最后一个索引

将数组转化为字符串：

- Array.toString() :   将数组转化为字符串， 使用逗号分隔每一项
- Array.join('分隔符')： 按照指定的分隔符 将所有的元素转化为字符串

连接多个数组：

- concat()： 返回一个新的数组，不影响原始数组

  ```
  var res = arr1.concat(arr2, arr3...);
  ```



数组截取：

- arr.slice(being, end)： 返回新数组， 左闭右开

数组删除：

- arr.splice(index， num):  表示删除开始的索引 以及要删除的个数， 此函数返回所删除的元素组成的新数组，并且会影响原始数组 



### String

JS 提供了三个特殊的引用类型： String， Number和Boolean，这三种类型由基本数据类型经过包装成为复杂数据类型，具有属性和方法。对于：

```
var str = "string"
```

其执行过程为：

```
var tmp = new String("string");  // 创建一个临时变量
str = tmp;  // 将这个临时变量赋值给 str
tmp = null;  // 销毁临时变量
```

字符串具有不可变性，如果对一个已经赋值的字符串重新赋值，实际上是新开辟一块内存，随后将变量名重新指向这块地址，因此不要对字符串进行大量的拼接操作，会出现效率问题，同样，对于String的所有方法，都不会修改原始的字符串，而是会返回一个新的字符串

- str.indexof('char'， index) ：要查找的字符 ，开始查找的位置，默认情况下是从头开始查找

返回字符串指定索引的字符：

- String.charAt(index)
- String.charCodeAt(index)  返回索引位置字符的ASCII码
- String[index]

字符串拼接与截取

- var res = str.concat(str1, str2, str3)， 在 str 基础上拼接多个字符串，返回拼接后的字符串，不会影响原始字符串，等效于 + 
- **str.substr(start index, length)：从 start索引 开始 取前 length 个字符**
- str.slice(start, end): 从 start 索引开始截取到 end 索引，类似功能的方法有 substring(start, end) 方法

字符串替换：

- var res  = str.replace('a', 'A')：将 'a‘ 替换为 'A' ，该方法只能替换字符串的第一个字符，如果需要全部改变，可以使用正则表达式实现，或者使用循环：

  ```javascript
  while(str1.indexOf('a')!=-1){
      str1 = str1.replace('a', 'A');
  }
  ```

字符串分割：

- var arr = str.split(',')， 方法参数为分隔符，与 arr.join() 方法互逆

字符串转化大小写：

- str.toUpperCase()
- str.toLowerCase()

## 简单数据类型和复杂数据类型

简单数据类型：变量中存放的是值，因此也叫值类型

- string、number、boolean、undefined、null
- 其中注意 null，赋值为 null 的变量，使用 `console.log(typeof val)`输出类型的时候，显示的结果是 object，在实际使用中，对于某个定义的变量，如果不确定其指向的对象，可以暂且取值为 null

复杂数据类型：变量中存放的是引用地址， 也称为引用数据类型

- 使用 new 关键字创建的对象，内置对象或者自定义对象等

内存分配：

- 栈：简单数据类型 -- 在栈中开辟一块空间，将值存放在这块空间中，然后让变量名指向这块空间
- 堆：复杂数据类型 -- 在栈中开辟一块空间，用于存放地址，该地址指向堆中的一块空间

其实 JS 本身没有堆栈的观念

在进行传参的时候，简单数据类型是将内存中的实际值复制了一份给形参，所以在方法中对形参进行改变不会影响原始的值，而复杂数据类型则是传递堆中的地址，因此会影响原始的对象







